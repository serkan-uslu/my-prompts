# Task Prompt — Feature Spec

## 🎯 Purpose
User story veya vague feature isteğini teknik spec'e çevirmek için. Kodlamaya başlamadan önce "ne build ediyoruz" sorusunu netleştirir.

## 📋 Context
- Design veya product ekibi olmayan solo / küçük ekip projeler için
- Çıktı: geliştiricinin direkt kullanabileceği teknik spec
- Claude, GPT, Gemini hepsinde çalışır

## 💬 The Prompt

```
Turn the following feature request into a technical specification.

Feature request: {user story or vague description}
Tech stack: {your stack}

Output format:

**What we're building**
One paragraph. Clarify scope — what is included and what is explicitly out of scope.

**User flow**
Step-by-step from the user's perspective. Number each step. No technical details here.

**Technical breakdown**
What needs to be built:
- UI: list of components and pages
- Data: new DB models or fields, changes to existing models
- Logic: Server Actions, API routes, or business rules
- State: what client state is needed

**Edge cases to handle**
Bullet list of "what happens when..." scenarios that aren't in the happy path.

**Open questions**
Things that need a decision before implementation starts.
Format: Q: {question} → Options: {option A} / {option B}

**Complexity estimate**
Low / Medium / High — with one sentence explaining why.

Rules:
- If the request is ambiguous, state your assumptions explicitly
- Do not write any code
- Complexity: Low = one dev, one day. Medium = one dev, 2–3 days. High = needs breakdown into sub-tasks.
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{feature_request}` | Feature isteği | "Users should be able to invite team members via email" |
| `{stack}` | Tech stack | `Next.js 14, Prisma, PostgreSQL, Resend` |

## 📝 Example Output

```
**What we're building**
Email-based team invitation flow. A user can invite others by email; invited users receive a link, click it, create an account (or log in), and join the workspace. Out of scope: role management (invitees are always Members), bulk invites.

**User flow**
1. User clicks "Invite Member" in team settings
2. Enters email address, clicks Send
3. System sends an email with a unique invite link (valid 7 days)
4. Recipient clicks link → if no account: registration form pre-filled with email → if account exists: auto-joins workspace
5. Both users see the new member in the team list

**Technical breakdown**
- UI: InviteModal component, InvitePending list in settings, AcceptInvite page (/invite/[token])
- Data: Invitation model (id, email, token, workspaceId, invitedById, expiresAt, acceptedAt?)
- Logic: createInvitation server action, sendInviteEmail via Resend, acceptInvitation server action
- State: no client state needed — all server-driven

**Edge cases**
- Email already has an account → auto-join, skip registration
- Email already a member → show error "already in workspace"
- Token expired → show expired page with "request new invite" option
- User clicks invite link while logged in as different account → warn + offer to switch

**Open questions**
Q: Can the same email be invited multiple times if they haven't accepted?
→ Options: Block re-invite / Invalidate old + send new

**Complexity estimate**
Medium — straightforward flow but involves email sending, token handling, and two different acceptance paths (new vs existing user).
```

## 🔄 Variations

**Sadece edge case üretmek için:**
```
I'm building: {feature description}
List every edge case I might have missed. Be exhaustive. Group by category.
```

**Sprint'e bölmek için:**
```
This spec is too large for one sprint. Break it into 2–3 sequential milestones, each shippable independently.

Spec: {paste spec}
```

---

*Last updated: 2026-03-02*
