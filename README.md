# Retool Workflow for ChatGPT SMS Interaction

This project provides a Retool Workflow for ChatGPT SMS interaction, allowing you to communicate with ChatGPT via SMS using a Twilio phone number. 

There are two versions of the workflow included here, and you can choose the one that best suits your needs. The first version is simpler and does not save context, while the second version includes context saving using a Retool database.

v1: Start with this version if you just want to try out ChatGPT SMS interaction without saving context history. 

v2: Use this version if you want to save context history and enable intelligent follow-up conversations with ChatGPT.

![Alt text](/assets/retool_workflow_chatgpt_demo.gif)

## Prerequisites

To use these Retool Workflows for ChatGPT SMS interaction, you will need:

1. Access to a Twilio account with an active phone number.
2. For version 2 of the workflow, you would need some sort of storage to save context history. The worklow inlcuded here utilizes Retool database, which is a built-in PostgreSQL-based database provided by Retool, but it can be any other storage. You would need to edit the workflow accordingly to accommodate to those database/storage resources.


## Importing the Workflow into Retool and Configuring the Twilio Blocks
To use the Retool Workflow for ChatGPT SMS interaction, you will need to import the workflow JSON file into your Retool account and configure the Twilio blocks. Here's how you can do it:

1. Download the JSON file for the workflow version that you want to use from the GitHub repository.
2. Log in to your Retool account and navigate to the Workflows section.
3. Click on New -> Import from JSON, and select the downloaded JSON file.
4. Open the imported workflow and navigate to the Twilio block (there are two of them, and you will need to edit them both)
5. Enter your Twilio Account SID and From phone number in the Configuration section.
6. Save the changes and you're done.
7. Now you can test the workflow by sending a text message to the configured Twilio phone number and checking the response in the Retool workflow.
8. Copy the URL provided by the Workflow as part of the Start Trigger Block. You will need to paste this into your Twilio phone number webhhok configuration. See next section. 

## Configuring a Twilio Phone Number and Webhook
To use the Retool Workflow for ChatGPT SMS interaction, you will need to configure a Twilio phone number and direct the webhook to the URL provided by the Retool workflow. Here's how you can do it:

1. Log in to your Twilio account and navigate to the Phone Numbers section.
2. If you don't have a phone number, click on Buy a Number and follow the instructions to purchase a new phone number. If you already have a phone number, click on it to edit its configuration.
3. Scroll down to the Messaging section and under A MESSAGE COMES IN, select Webhook from the dropdown menu
4. Paste the Retool webook URL (see previous section) into the Webhook URL field in the Twilio configuration.
5. Save the configuration and you're done.

## Creating the table in Retool Database (required for v2 workflow only)
To use version 2 of the workflow, you will need to configure the Retool database with the fields mentioned above. 

For this workflow to work, we only need one table with the following fields:

- phone_number  (Text) (primary key)
- message_sid (Text)
- context (Text)
- prompt_history (Text)
- active_conversation (Boolean) 
- created_at (TIMESTAMPTZ)
- updated_at (TIMESTAMPTZ)

There are a couple ways you can create this table in Retool Database

1. Create the table manually in Retool Database (click on Database once logged into your Retool account)
2. Automatically create it using this workflow. Here's the [JSON for this workflow](https://github.com/ajot/retool_chatgpt_twilio_workflow/blob/main/sql_query_to_create_chat_log_table_in_retool_database.json) that you can import, and execute from your Retool account. Here's the SQL query this workflow executes -

```
CREATE TABLE chat_log (
    phone_number TEXT PRIMARY KEY,
    message_sid TEXT,
    context TEXT,
    prompt_history TEXT,
    active_conversation BOOLEAN,
    created_at TIMESTAMPTZ,
    updated_at TIMESTAMPTZ
);
```
![Alt text](https://github.com/ajot/retool_chatgpt_twilio_workflow/blob/main/CleanShot%202023-04-28%20at%2009.29.41.png)

3. Create the table by importing [this CSV file](https://github.com/ajot/retool_chatgpt_twilio_workflow/blob/main/retool_chatgpt_log_database_template.csv).

---

## How it works

### Version 1: Simple ChatGPT SMS Interaction (no context saving)
This workflow allows you to send a text message to a Twilio phone number, which triggers a Retool webhook when an incoming text message is received. The contents of this message are then passed on to the [Smart Block](https://retool.com/blog/gpt4-in-retool), which is a block provided by Retool to interact with the ChatGPT API. The response received from ChatGPT is then sent back to the original sender using the Twilio block. This version of the workflow does not save context, so you cannot have intelligent follow-up conversations with it.

During Smart Block's beta, Retool includes free access to the Smart Block to explore GPT-4 applications. So, you dont' need to have an API key, but if you'd like to use your own, you can include that in the SmartBlock once you have imported the JSON to your Retool account.


### Version 2: ChatGPT SMS Interaction with Context Saving


![Alt text](/assets/workflow_canvas.png)

This workflow enables intelligent follow-up conversations with ChatGPT by saving context history using Retool database. You can switch to another database/storage of your choice, and edit the workflow accordingly. Here's a list of native [data integrations](https://retool.com/integrations/) supported by Retool.


When an incoming text message is received, the workflow performs the following steps:

1. If the message is a "new chat" request, the workflow sends a confirmation message to the sender, sets the active conversation flag to true, and ends the conversation.
2. If the message is NOT a "new chat" request, the workflow checks if there is an active conversation for the sender's phone number.
3. If there is an active conversation, the workflow calls the ChatGPT API with the context history from the database, updates the context history in the database, and sends the response back to the sender.
4. If there is no active conversation, the workflow creates a new active conversation in the database, calls the ChatGPT API without the context history, and sends the response back to the sender.


