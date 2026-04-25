# Smart Complaint System: Technical Architecture

This document provides a comprehensive overview of the architecture and logic behind the Agentic AI-Powered Smart Complaint System.

## 1. System Overview
The Smart Complaint System is a production-ready MERN (MongoDB, Express, React, Node.js) application designed to automate the lifecycle of student complaints using Agentic AI.

### Tech Stack
- **Frontend**: React (Vite) + TypeScript + Tailwind CSS (Aesthetic/Responsive UI).
- **Backend**: Node.js + Express + TypeScript.
- **Database**: MongoDB (NoSQL) for flexible data modeling.
- **AI Engine**: LangChain.js + OpenAI (GPT-4o-mini).
- **Real-time**: Socket.io for instant dashboard updates.
- **Communications**: Nodemailer (Email) and Twilio (SMS/Voice).

---

## 2. AI Agent Logic (The "Brain")
The core innovation of this system is the **Agentic Workflow** implemented in `aiService.ts`.

### Hybrid Classification & Prioritization
To ensure 100% reliability even if AI services are unavailable or quota is exceeded, we use a **Hybrid RAG-style Fallback System**:

1.  **Strict Validation**: AI first checks if the complaint is "Silly" or "Fake". If it detects nonsense (e.g., "Aliens in lab"), it rejects the complaint instantly.
2.  **Keyword-Based Routing (RAG Base)**: The system checks for critical keywords (Fire, Smoke, Short Circuit, etc.) to immediately flag High-Priority issues without waiting for AI processing.
3.  **LLM Deep Analysis**: If the text is nuanced, the OpenAI agent analyzes:
    - **Category**: Infrastructure, Hostel, Technical, Emergency, or Other.
    - **Priority**: High (1h SLA), Medium/High (4h SLA), Medium (24h SLA), Low (72h SLA).
    - **Sentiment**: Detects panic, frustration, or neutral tones.
    - **Action**: Generates a recommended immediate action for the admin.

---

## 3. Communication & Automation Flow
The system is designed to be proactive rather than reactive.

### Notification Layers
- **Tier 1 (Instant)**: Admin receives a detailed **Email** (Name, Email, Phone, Category, Text) and real-time dashboard alert. (SMS disabled for routine complaints).
- **Tier 2 (Continuous Pinging)**: For **High-Priority** items, a cron job sends a reminder **Email** every 3 minutes including full complainant details.
- **Tier 3 (SOS)**: Only for life-threatening emergencies, triggers a Twilio-powered **Voice Call or SMS**.
- **Tier 4 (Resolution)**: User receives a confirmation email showing the Admin's Name, Admin's Email, and the Resolution Message.

---

## 4. SLA & Escalation
- **SLA Tracking**: Every complaint has a calculated deadline based on its priority.
- **Visual Alerts**: The Admin Dashboard highlights "OVERDUE" tickets in red with a pulsing animation to ensure they are prioritized.
- **Background SLA Checker**: A dedicated service (`slaService.ts`) monitors all open tickets and triggers alerts if a deadline is missed.

---

## 5. Security & Authentication
- **JWT Auth**: Secure login and registration using JSON Web Tokens.
- **Role-Based Access Control (RBAC)**: 
    - `student`: Can submit and track their own complaints.
    - `admin`: Can view all complaints, assign resolvers, and resolve tickets.
    - `warden`: Specialized view for hostel-related issues.
- **Protected Routes**: React `ProtectedRoute` component ensures users only see authorized dashboards.
