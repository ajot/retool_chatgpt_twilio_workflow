# Retool Workflow for ChatGPT SMS Interaction

This project provides a Retool Workflow for ChatGPT SMS interaction, allowing you to communicate with ChatGPT via SMS using a Twilio phone number. 

There are two versions of the workflow included here, and you can choose the one that best suits your needs. The first version is simpler and does not save context, while the second version includes context saving using a Retool database.

![Alt text](/assets/retool_workflow_chatgpt_demo.gif)

![Alt text](/assets/workflow_canvas.png)

## Prerequisites

To use these Retool Workflows for ChatGPT SMS interaction, you will need:

1. Access to a Twilio account with an active phone number.
2. For version 2 of the workflow, you would need some sort of storage to save context history. The worklow inlcuded here utilizes Retool database, which is a built-in PostgreSQL-based database provided by Retool, but it can be any other storage. You would need to edit the workflow accordingly to accommodate to those database/storage resources.

## Accessing ChatGPT 
During Smart Block's beta, Retool includes free access to the Smart Block to explore GPT-4 applications. So, you dont' need to have an API key, but if you'd like to use your own, you can include that in the SmartBlock once you have imported the JSON to your Retool account.

---

### Version 1: How it works

The first version of the workflow allows you to send a text message to a Twilio phone number, which triggers a Retool webhook when an incoming text message is received. The contents of this message are then passed on to the SmartBlock, which is a block provided by Retool to call the ChatGPT API. After this, the response is received from ChatGPT, and then sent back to the original sender using the Twilio block. This version of the workflow does not save context, so you cannot have intelligent follow-up conversations with it.

### Version 2: How it works

The second version of the workflow includes context history using a Retool database, and writing SQL queries to update the database with the prompt and response history from ChatGPT. This version of the workflow enables intelligent follow-up conversations with ChatGPT. The database has the following fields:

- phone_number (primary key)
- message_sid
- context
- prompt_history
- active_conversation
- created_at
- updated_at

When an incoming text message is received, the workflow performs the following steps:

1. If the message is a "new chat" request, the workflow sends a confirmation message to the sender, sets the active conversation flag to true, and ends the conversation.
2. If the message is not a "new chat" request, the workflow checks if there is an active conversation for the sender's phone number.
3. If there is an active conversation, the workflow calls the ChatGPT API with the context history from the database, updates the context history in the database, and sends the response back to the sender.
4. If there is no active conversation, the workflow creates a new active conversation in the database, calls the ChatGPT API without the context history, and sends the response back to the sender.

To use version 2 of the workflow, you will need to configure the Retool database with the fields mentioned above. You can use the [Retool workflow to set up the table](https://retool.com/template/create-table-in-database/), or [import this CSV file](https://github.com/retoolhq/chatgpt-sms-interaction/blob/main/retool_database_fields.csv).

