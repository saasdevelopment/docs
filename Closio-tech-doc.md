# How Closio.ai Works

A comprehensive guide explaining the technical logic behind Closio.ai for non-technical readers.

---

## Table of Contents

1. [Overview](#overview)
2. [The Message Journey](#the-message-journey)
3. [How the AI Understands Your Business](#how-the-ai-understands-your-business)
4. [Credit System Explained](#credit-system-explained)
5. [Facebook Integration](#facebook-integration)
6. [Plans & Billing](#plans--billing)
7. [Key Technical Concepts](#key-technical-concepts)

---

## Overview

Closio.ai is an AI-powered auto-reply system for Facebook Page Messenger and ad comments. When someone messages your Facebook Page or comments on your ad, Closio automatically generates and sends a personalized response using AI.

### The Simple Version

```
Customer sends message → Closio receives it → AI generates reply → Reply sent to customer
```

### What Makes It Work

1. **Facebook Webhook** - Facebook notifies Closio instantly when messages arrive
2. **AI (OpenAI GPT)** - Generates human-like responses based on your business info
3. **Knowledge Base** - Your product information that teaches the AI what to say
4. **Credits** - Pay-per-use system that tracks AI usage

---

## The Message Journey

Here's exactly what happens when a customer messages your Facebook Page:

### Step 1: Customer Sends Message

A potential buyer sees your Facebook ad and clicks "Send Message" or DMs your page directly.

### Step 2: Facebook Notifies Closio (Webhook)

Facebook has a system called "Webhooks" - think of it as a doorbell. When a message arrives, Facebook rings Closio's doorbell with the message details.

```
What Facebook sends:
- Sender ID (who sent the message)
- Page ID (which of your pages received it)
- Message text
- Timestamp
- Message type (text, image, etc.)
```

### Step 3: Message Classification

Before responding, Closio quickly analyzes the message to determine:

- **Is it a question?** → Needs informative answer
- **Is it an objection?** → Needs persuasive response
- **Is it ready to buy?** → Guide to checkout
- **Is it spam/irrelevant?** → Skip or generic response

This classification costs a tiny amount of credits (uses AI).

### Step 4: Knowledge Retrieval (RAG)

RAG = "Retrieval Augmented Generation" (fancy term for "look up relevant info before answering")

**How it works:**

1. Take customer's question: *"How much does shipping cost?"*
2. Search your knowledge base for shipping-related info
3. Find relevant chunks: *"Free shipping on orders over $50. Standard shipping $5.99..."*
4. Give this context to the AI along with the question

**Why this matters:** Without RAG, the AI would make up answers. With RAG, it gives accurate info from YOUR business data.

### Step 5: AI Response Generation

The AI (GPT-4) receives:

- Customer's message
- Relevant knowledge chunks from your business
- Your page settings (tone, business name, etc.)
- Conversation history (previous messages)

It then generates a response that:
- Answers the question accurately
- Matches your brand voice
- Guides toward a sale
- Handles objections

### Step 6: Response Sent

Closio sends the AI-generated response back to Facebook, which delivers it to the customer. The customer sees it as a normal message from your page.

### Step 7: Credits Deducted

Credits are deducted based on:
- Input tokens (the message + context sent to AI)
- Output tokens (the response generated)
- Fixed per-reply cost

---

## How the AI Understands Your Business

The AI isn't born knowing about your products. You teach it through the **Knowledge Base**.

### Adding Knowledge

You provide product URLs, and Closio:

1. **Scrapes the page** - Visits the URL and extracts all text content
2. **Chunks the content** - Splits long content into smaller pieces (~500 words each)
3. **Creates embeddings** - Converts text into numbers (vectors) that AI can search

### What Are Embeddings?

Imagine converting every sentence into a point on a map. Similar sentences end up close together.

```
"Free shipping available" → [0.23, 0.87, 0.12, ...]
"We offer free delivery" → [0.25, 0.85, 0.14, ...]  ← Close to above!
"Return policy is 30 days" → [0.91, 0.32, 0.76, ...] ← Far from above
```

When a customer asks about shipping, Closio finds the closest knowledge chunks by comparing these numbers.

### Knowledge Flow

```
Your product page → Scraped text → Chunks → Embeddings → Stored in database
                                                              ↓
Customer question → Question embedding → Find similar chunks → AI uses them
```

---

## Credit System Explained

Credits are Closio's currency. Instead of unlimited usage (which would be expensive), you pay for what you use.

### How Credits Work

| Action | Credit Cost |
|--------|-------------|
| One AI reply | ~1 credit |
| Knowledge processing | Varies by size |
| Message classification | ~0.05 credits |

### Why Credits Instead of Unlimited?

AI costs money for every request. OpenAI charges per token (roughly per word). Credits let us:
- Keep pricing fair (light users pay less)
- Prevent abuse
- Scale sustainably

### Credit Math Example

**Growth Plan: 2,000 credits/month**

```
2,000 credits ÷ 1.04 credits per reply ≈ 1,920 AI replies per month
```

That's about 64 replies per day - enough for most growing businesses.

### What Uses Credits?

1. **AI Reply Generation** (main cost)
   - Input: Your knowledge + message + history
   - Output: The generated response

2. **Knowledge Processing**
   - When you add product URLs
   - One-time cost per URL

3. **Message Classification**
   - Small cost to analyze intent
   - Helps route messages properly

---

## Facebook Integration

### How Closio Connects to Facebook

1. **OAuth Authentication**
   - You click "Connect Facebook"
   - Facebook asks "Allow Closio to manage your pages?"
   - You approve → Closio gets an access token

2. **Access Tokens**
   - Like a key to your page
   - Allows reading messages and sending replies
   - Encrypted and stored securely

3. **Webhook Subscription**
   - Closio tells Facebook: "Send me all messages for this page"
   - Facebook delivers messages in real-time

### Two Facebook Apps (Why?)

Closio uses two separate Facebook apps:

| App | Purpose | Permissions |
|-----|---------|-------------|
| Login App | User authentication | email, profile |
| Page App | Message handling | pages_messaging, pages_manage_metadata |

**Why separate?** Cleaner permission scopes. Login only needs basic info. Page management needs extensive permissions.

### Message Types Handled

- **Direct Messages (DMs)** - Private conversations
- **Ad Comments** - Public comments on your ads (replied privately)
- **Page Comments** - Comments on your page posts

---

## Plans & Billing

### Plan Structure

| Plan | Credits | Pages | Price |
|------|---------|-------|-------|
| Starter | 500 | 3 | $29/mo |
| Growth | 2,000 | 8 | $79/mo |
| Scale | 6,000 | 20 | $199/mo |

### How Billing Works

1. **Stripe Integration**
   - Secure payment processing
   - Automatic monthly billing
   - Cancel anytime

2. **Subscription Flow**
   ```
   Select plan → Stripe checkout → Payment confirmed → Credits added → Subscription active
   ```

3. **Credit Renewal**
   - Credits reset each billing cycle
   - Unused credits don't roll over
   - Upgrade anytime for more credits

### Custom Plans (Offline)

For enterprise users, admins can create custom plans:
- Custom credit amounts
- Custom page limits
- Offline billing (invoice-based)
- Manual credit additions

---

## Key Technical Concepts

### Tokens

**What:** The smallest unit of text AI processes. Roughly 1 token = 4 characters or ¾ of a word.

**Example:**
```
"Hello, how can I help you today?" = 9 tokens
```

**Why it matters:** AI costs are calculated per token. More tokens = more cost.

### Webhooks

**What:** A way for one service to notify another when something happens.

**Analogy:** Like a doorbell. Instead of constantly checking "did anyone message me?", Facebook rings the doorbell (webhook) when a message arrives.

**Technical:** HTTP POST request from Facebook to Closio's server with message data.

### Embeddings / Vectors

**What:** Numbers representing the meaning of text.

**Analogy:** GPS coordinates for meaning. "Happy" and "Joyful" have similar coordinates. "Happy" and "Refrigerator" have very different coordinates.

**Why:** Allows finding similar content without exact keyword matching. Customer asks "delivery time?" and finds knowledge about "shipping duration" even though words are different.

### RAG (Retrieval Augmented Generation)

**What:** Look up relevant information before generating a response.

**Without RAG:**
```
Customer: "What's your return policy?"
AI: "I don't know your specific return policy." (Makes something up)
```

**With RAG:**
```
Customer: "What's your return policy?"
System: [Finds knowledge chunk about returns]
AI: "We offer 30-day hassle-free returns! Just..." (Accurate answer)
```

### Queue System (BullMQ)

**What:** A waiting line for tasks.

**Why needed:** Messages might arrive faster than we can process them. The queue ensures:
- No messages are lost
- Processing happens in order
- Retries if something fails

**Flow:**
```
Message arrives → Added to queue → Worker picks it up → Processes → Next message
```

### Encryption

**What:** Scrambling data so only authorized systems can read it.

**What's encrypted:**
- Facebook access tokens
- User passwords (hashed, not encrypted)
- Sensitive settings

**Why:** If database is compromised, attackers can't use stolen tokens.

---

## System Architecture (Simple View)

```
┌─────────────────────────────────────────────────────────────────┐
│                         CUSTOMER                                │
│                    (Facebook Messenger)                         │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                         FACEBOOK                                │
│              (Receives message, sends webhook)                  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      CLOSIO SERVER                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │   Webhook    │→ │    Queue     │→ │   AI Worker  │          │
│  │   Handler    │  │   (BullMQ)   │  │  (OpenAI)    │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
│         │                                    │                  │
│         ▼                                    ▼                  │
│  ┌──────────────┐                    ┌──────────────┐          │
│  │   Database   │◄───────────────────│  Knowledge   │          │
│  │  (Postgres)  │                    │   Base RAG   │          │
│  └──────────────┘                    └──────────────┘          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                         FACEBOOK                                │
│                    (Delivers AI reply)                          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                         CUSTOMER                                │
│                     (Receives reply)                            │
└─────────────────────────────────────────────────────────────────┘
```

---

## Common Questions

### Why does my reply sometimes take a few seconds?

The AI needs time to:
1. Retrieve relevant knowledge (100-200ms)
2. Generate response (500-2000ms)
3. Send via Facebook API (100-300ms)

Total: 1-3 seconds is normal.

### Can the AI make mistakes?

Yes. AI can:
- Occasionally misunderstand context
- Give generic answers if knowledge base lacks info
- Rarely hallucinate (make up info)

**Mitigation:** Good knowledge base + proper settings + monitoring conversations.

### What if I run out of credits?

Auto-replies pause until:
- You upgrade your plan
- New billing cycle adds credits
- Admin manually adds credits

Messages are still received and stored - they just won't get auto-replies.

### Is my data secure?

- Tokens encrypted at rest
- Passwords hashed (can't be reversed)
- HTTPS everywhere
- Database access restricted
- No selling of customer data

---

## Glossary

| Term | Simple Definition |
|------|-------------------|
| API | A way for programs to talk to each other |
| Token | A small piece of text (roughly a word) |
| Webhook | An automatic notification when something happens |
| Embedding | Numbers representing the meaning of text |
| RAG | Looking up info before answering |
| OAuth | Secure way to grant access without sharing passwords |
| Queue | A waiting line for tasks |
| Scraping | Automatically reading content from a webpage |

---

*Last updated: January 2026*
