
To recreate this diagram:
1. Use [draw.io](https://draw.io) or [Lucidchart].
2. Add icons for each AWS service used.
3. Show arrows from left to right reflecting the request flow.
4. Use dashed line to indicate conditional transfer to agent queue.

---

##  How It Works (Flow Summary)

1. **User calls in via Amazon Connect.**
2. A **Lex bot** greets and asks for intent ("What can I help you with today?").
3. If the intent is:
   - **CreateTicketIntent**: Triggers Lambda → Writes to DynamoDB.
   - **CheckHoursIntent**: Lex responds with pre-configured text.
4. If Lex can't understand: fallback → prompt + optional transfer to live agent queue.

---

## 📸 Screenshots

Here are some visual highlights from the deployment:

| Description | Screenshot |
|------------|------------|
| ✅ Amazon Connect Flow (Play Prompt, Get Input, Route) | `Amazon-Connect-AWS-Lex-Flow.png` |
| ✅ Amazon Lex Intents Setup | `Amazon-lex-intent.png` |
| ✅ Lambda Function (Python) | `Lambda-python-script.png` |
| ✅ DynamoDB Table (`SupportTickets`) | `DynamoDB-table.png` |
| ✅ Lex Bot Alias Setup (`LiveBotAlias`) | `Lex-Bot-Alias.png` |

*(Place these images in a `screenshots/` folder or link them from your repo.)*

---

## 📋 Setup Steps (Summary)

1. **Amazon DynamoDB**: Create table `SupportTickets`  
   - Partition key: `ticketId` (String)
   - Additional attributes: `issue`, `timestamp`

2. **AWS Lambda**:  
   - Create a function in Python
   - Add IAM permissions for DynamoDB, Lex
   - Sample logic: create ticket + store in DynamoDB

3. **Amazon Lex V2**:  
   - Create bot `CustomerSupportBot`
   - Add intents: `CreateTicketIntent`, `CheckhoursIntent`
   - Deploy bot and create alias `LiveBotAlias`

4. **Amazon Connect**:  
   - Set up a free-tier instance
   - Create contact flow:
     - Play Welcome Prompt
     - Get Customer Input → Lex
     - Check Intents → Lambda or Route
     - Add fallback prompt → transfer to agent (via queue)

---

## ❗ Challenges Encountered

- **Lex Alias Not Showing in Connect**:  
  Initially, the alias did not appear when trying to link the Lex bot in the Connect flow. This was solved by **deploying the Lex bot to a version** and creating a new alias (`LiveBotAlias`).

- **Fallback Logic Confusion**:  
  The Connect flow required properly routing the fallback prompt → to the "Set Working Queue" block → then "Transfer to Queue".

- **DynamoDB Attribute Misunderstanding**:  
  While the primary key was `ticketId`, I initially forgot to add `issue` and `timestamp` fields manually when inserting records.

---

##  Testing the Bot

- ☎️ Test call via Amazon Connect
- 🗣️ Spoken input: "I want to report an issue"
- ✅ Lambda function triggered
- 🗃️ Ticket saved in DynamoDB
- ❓ Unknown input: fallback prompt plays
- 📞 Option to connect to live agent confirmed

---

## 📦 Future Improvements

- Add FAQ database with vector search via Bedrock + OpenSearch.
- Implement voice logging using Amazon Transcribe.
- Add email follow-up using Amazon SES.
- Deploy as Infrastructure-as-Code using Terraform.

---

## 📃 License

MIT License © 2025

---# AI-Powered-Customer-Support
