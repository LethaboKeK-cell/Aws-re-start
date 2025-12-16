# Amazon Lex V2 Quiz Bot Creation

A step‑by‑step, first‑person walkthrough of how I built an Amazon Lex V2 bot that welcomes users and runs a multiple‑choice S3 quiz using AWS Lambda for fulfillment.

---

## Table of Contents
- [Setting up the bot](#setting-up-the-bot)
- [Designing intents and utterances](#designing-intents-and-utterances)
- [Building multi-turn dialogs](#building-multi-turn-dialogs)
- [Integrating AWS Lambda](#integrating-aws-lambda)
- [Testing and debugging](#testing-and-debugging)
- [Architecture summary](#architecture-summary)
- [Image placement checklist](#image-placement-checklist)

---

## Setting up the bot

I wanted a clean, reproducible foundation with least‑privilege IAM and defaults that don’t get in the way of faster iteration.

1. **Open Lex V2 and create a blank bot.**  
   - I chose *Traditional creation* with a blank bot to control every part of the conversational design.  
   - I let Lex create a new IAM role with basic permissions to avoid misconfigurations early on.

![001](assets/Screenshot%202025-12-16%20200148.png)

2. **Configure base settings.**  
   - I named the bot clearly, added a short description, and set COPPA appropriately.  
   - I kept the default idle session timeout and added tags for resource tracking.

3. **Add a language.**  
   - I selected English (US) and chose a voice for speech responses.  
   - I kept the default intent classification confidence threshold.

4. **Review the bot overview.**  
   - With language and permissions in place, the bot was ready for intents, slots, and utterances.

---

## Designing intents and utterances

Clear intent boundaries and natural utterances make the bot robust and easy to maintain.

1. **GreetingIntent**  
   - Utterances: “Hello”, “Hi”, “Hey there”, “Good Morning”  
   - Purpose: Welcoming users and initiating conversation

![002](assets/Screenshot%202025-12-16%20200257.png)
![003](assets/Screenshot%202025-12-16%20200312.png)

2. **QuizIntent**  
   - Utterances: “Start quiz”, “I want to take the quiz”, “Ask me S3 questions”, “Begin the quiz”, “Let’s start”  
   - Purpose: Launching the S3 knowledge quiz

![004](assets/Screenshot%202025-12-16%20200357.png) 
![005](assets/Screenshot%202025-12-16%20200410.png)

3. **Slot strategy**  
   - For multiple‑choice answers, I planned a custom slot type with values like A, B, C, D.  
   - Validation handled in Lambda for consistency.

---

## Building multi-turn dialogs

Multi‑turn keeps the conversation focused, one question at a time, and allows immediate feedback.

- Flow: User triggers QuizIntent → bot presents Question 1 → user answers → bot validates and responds → next question.  
- Session attributes: `quizActive`, `currentIndex`, `score` to track state across turns.

---

## Integrating AWS Lambda

Lambda gives me precise control over logic, validation, and scoring.

1. **Create the Lambda function.**  
   - Function name: `QuizBotFulfilment`  
   - Region: `us-east-1`  
   - Runtime: Python

![007](assets/Screenshot%202025-12-16%20201449.png)

2. **Grant Lex permissions.**  
   - Resource‑based policies allow Lex to invoke Lambda securely.  
   - Principals: `lex.amazonaws.com` and `lexv2.amazonaws.com`

![008](assets/Screenshot%202025-12-16%20201516.png)

3. **Wire Lambda to intent fulfillment.**  
   - Enabled “Use a Lambda function for fulfillment” in Lex.  
   - Kept fulfillment updates configurable.

![006](assets/Screenshot%202025-12-16%20200333.png)

4. **Implement quiz logic in Python.**  
   - Defined `QUIZ_QUESTIONS` list with id, question, options, and correctAnswer.  
   - Helpers: `start_quiz()`, `handle_quiz_answer()`, `build_response()`  
   - Persisted session attributes on every reply.

![009](assets/Screenshot%202025-12-16%20201547.png)

---

## Testing and debugging

1. **Build and test in Lex console.**  
   - Used utterances like “Start quiz” and “A”.  
   - Verified JSON interactions and session attributes.

2. **Edge cases.**  
   - Invalid inputs reprompted gracefully.  
   - Completion logic gave final scores and feedback.

3. **Troubleshooting.**  
   - Checked fulfillment settings and ARN linkage.  
   - Standardized responses with `build_response()`.  
   - Logged events in CloudWatch for debugging.

---

## Architecture summary

| Component              | Description                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| **Intents**            | GreetingIntent, QuizIntent                                                  |
| **Utterances**         | Natural phrases like “Hello”, “Start quiz”, “Ask me S3 questions”           |
| **Lambda Function**    | `QuizBotFulfilment` with structured quiz logic and session tracking         |
| **Permissions**        | Resource-based policies allowing Lex to invoke Lambda securely              |
| **Quiz Content**       | Stored in Python list and JSON file, supports multiple-choice format        |
| **Session Attributes** | Track quiz state: `quizActive`, `currentIndex`, `score`                     |
| **Error Handling**     | Friendly reprompts for invalid input, logs for debugging                    |
| **Testing**            | JSON inspection, edge case validation, user acceptance testing              |

---
