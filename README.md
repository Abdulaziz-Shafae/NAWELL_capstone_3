# NAWELL | ناول

NAWELL | ناول is a skill-exchange platform that helps individuals and companies learn without relying only on paid lessons. Users earn tokens by teaching and spend them on learning. Skill verification, AI matching, date negotiation, sessions, and reviews help users find suitable providers and organize their exchanges.

## Main features

- **Accounts and profiles:** individual and company registration, session-based login/logout, email verification, profile views, and dashboards.
- **Skills and assessments:** a skill catalog, account skills, assessment history, skill levels, and verification.
- **Teaching offers:** providers offer verified skills with token costs, capacity, and online/on-site delivery modes.
- **Learning requests:** learners request active offers and view requests by skill, requester, provider, urgency, or status.
- **Negotiations:** learners and providers exchange messages and propose dates; urgency and weekend rules can affect the token price.
- **Exchanges:** participants create, accept, cancel, and complete exchanges, with token reservation, refunds, and provider credits.
- **Agreements:** provider and learner acceptance flags, acceptance status, and AI-assisted agreement drafting.
- **Sessions:** individual and group sessions, participant enrollment, attendance, offer/exchange lookups, and Zoom meeting creation.
- **Reviews:** reviews between exchange participants, account review lists, and average ratings.
- **Tokens:** balance, transaction history, teaching bonuses, refunds, and simulated purchase/redemption.
- **Search:** find providers and learning requests by skill.
- **Notifications:** email and WhatsApp updates for learning requests, negotiation messages, exchanges, and refunds; session emails and automated reminders.

## Typical learning journey

1. Register an account, log in, and verify the email address.
2. Add skills to the account and take an assessment. Passing an assessment can verify the skill and update its level.
3. Publish an offer for a verified skill, or search for another provider's offers.
4. Create a learning request against an active offer.
5. Negotiate a date and accept a proposal, including any applicable urgency or weekend cost.
6. Create and accept an exchange. The acceptance workflow reserves the learner's tokens.
7. Create or join a session, optionally create a Zoom meeting, and record attendance.
8. The learner confirms exchange completion; reserved tokens are credited to the provider.
9. Review the other participant and track token history or eligible teaching bonuses.

Agreement acceptance is tracked separately; the current exchange workflow does not require both agreement acceptance flags before completion.

## Token rules

| Rule                     | Current behavior                                                              |
| ---                      | ---                                                                           |
| Starting balance         | New accounts start with 3 tokens                                              |
| Offer price              | Each offer sets its token cost                                                |
| Urgency                  | An earlier proposed date can add 1–3 tokens under the negotiation calculation |
| Weekend                  | Friday or Saturday proposals add 1 token                                      |
| Exchange acceptance      | Reserves tokens from the learner's balance                                    |
| Exchange cancellation    | Refunds reserved tokens where applicable                                      |
| Exchange completion      | Credits the provider after learner confirmation                               |
| Teaching bonus           | 5 bonus tokens per eligible group of 5 completed teachings                    |
| Purchase/redemption rate | 1 token equals 10 SAR in the simulation                                       |

Purchase and redemption update local balances and transaction records. They do not process real payments or payouts.

## AI and external integrations

| Service  | Features                                                                                                                                                                                                                    |
| ---      | ---                                                                                                                                                                                                                         |
| OpenAI   | Provider matching, match explanations, CV skill extraction, offer suggestions, skill relationships, related providers, assessment generation/evaluation, exchange fairness, agreement drafting, and LinkedIn skill matching |
| Apify    | Retrieves LinkedIn profile data for the LinkedIn skill workflow                                                                                                                                                             |
| Brevo    | Verification, learning-request, negotiation, exchange, token, and session emails, plus scheduled session reminders                                                                                                          |
| Zoom     | OAuth token retrieval and meeting creation                                                                                                                                                                                  |
| Ultramsg | WhatsApp notifications for learning requests, negotiation messages, exchange creation/acceptance/cancellation, and cancellation refunds                                                                                     |

The LinkedIn workflow uses Apify rather than the official LinkedIn API. AI feature routes include supporting import operations; not every route calls a model on every request.

## Notification workflows

- A new learning request notifies the provider by email and WhatsApp.
- A negotiation response notifies the other participant by email and WhatsApp; proposal acceptance sends an email.
- Exchange creation and acceptance notify the relevant participant by email and WhatsApp.
- Cancellation sends participant updates and, when tokens were reserved, a WhatsApp refund confirmation to the learner.
- New sessions notify learners with accepted or in-progress exchanges for the offer; joining sends a confirmation email.
- Exchange completion sends completion emails to the learner and provider.
- A scheduled job checks every minute and sends session reminder emails to enrolled learners during the hour before the session starts.

Notifications run inside existing workflows or the scheduled reminder job. They are not separate HTTP endpoints.

## Technology and structure

Java 17, Spring Boot, Spring MVC, Spring Data JPA/Hibernate, MySQL, Jakarta Validation, Lombok, Maven, the OpenAI Java SDK, Apache PDFBox, and Jackson.

```text
Capstone_3/
├── pom.xml
├── mvnw / mvnw.cmd
└── src/main/
    ├── java/com/example/capstone_3/
    │   ├── Controller/   HTTP routes
    │   ├── Service/      Business logic and integration helpers
    │   ├── Repository/   Database access interfaces
    │   ├── Model/        JPA entities
    │   ├── DtoIn/        Request data
    │   ├── DtoOut/       Response data
    │   ├── Config/       Application configuration
    │   ├── Advice/       Exception handling
    │   └── Api/          Response and exception types
    └── resources/application.properties
```

## Database entities

| Area                    | Entities                                         |
| ---                     | ---                                              |
| Accounts and profiles   | Account, IndividualProfile, CompanyProfile       |
| Skills and offers       | Skill, AccountSkill, SkillAssessment, SkillOffer |
| Requests and exchanges  | LearningRequest, RequestNegotiation, Exchange    |
| Agreements and sessions | Agreement, Session, SessionParticipant           |
| Feedback and tokens     | Review, TokenTransaction                         |

## API conventions

- JSON request/response bodies are used for most endpoints.
- CV upload uses `multipart/form-data` with a `file` field.
- Login stores the account ID in an HTTP session. Preserve the session cookie when calling routes that require login.
- IDs in route paths identify skills, offers, requests, exchanges, sessions, and account skills.
- Bean validation and controller advice handle request validation and application exceptions.

This is a capstone implementation. Route counts describe implemented source routes, not a guarantee of runtime correctness. Production deployment requires further security work, including password hashing and consistent authorization on raw CRUD routes.

## Endpoint summary

| Category                   | Count   |
| ---                        | ---:    |
| Basic CRUD                 | 43      |
| Extra business features    | 66      |
| AI feature routes          | 12      |
| Dedicated other API routes | 2       |
| **Total**                  | **123** |

Each HTTP method plus full route is counted once. Basic CRUD includes ordinary field mapping, defaults, record/relationship existence checks, and uniqueness checks. Endpoints with additional business rules—such as pricing, scoring, eligibility, token grants, lifecycle restrictions, or cross-record effects—are classified as Extra. AI and dedicated integration routes have their own categories.


## Abdulaziz Shafae Contributions

The route inventory credits the first recorded introduction in Git. Later changes may involve shared work.

**Entities:** Account, IndividualProfile, CompanyProfile, Exchange, RequestNegotiation.

**Main work:** account/profile services, login/logout, registration, starting tokens, email verification, dashboards, negotiation proposals and pricing, exchange creation/acceptance/cancellation/details, and learning-request details.

**AI:** exchange fairness, agreement generation, LinkedIn skill extraction, and LinkedIn skill import.

**Integrations:** Brevo verification email, Apify LinkedIn scraping, and Zoom meeting creation. Apify-backed routes are included in the AI feature count.

**Notification work:** email and WhatsApp updates for new learning requests, negotiation responses, exchange creation, acceptance, and cancellation; proposal acceptance emails and cancellation confirmation/refund emails.

**Configuration ownership:** email API configuration used by the team.

**Additional contributions:** shared account validation, token reservation/completion improvements, session/review ownership checks, integration safeguards, and merge/conflict resolution.


| Basic CRUD | Extra | AI   | Other API | Total endpoints | Entity classes |
| ---:       | ---:  | ---: | ---:      | ---:            | ---:           |
| 18         | 19    | 4    | 2         | **43**          | 5              |

### Extra endpoints

| Method | Route                                                                       | Handler                  |
| ---    | ---                                                                         | ---                      |
| POST   | `/api/v1/account/add`                                                       | `add`                    |
| POST   | `/api/v1/account/login`                                                     | `login`                  |
| POST   | `/api/v1/account/logout`                                                    | `logout`                 |
| POST   | `/api/v1/account/register/individual`                                       | `registerIndividual`     |
| POST   | `/api/v1/account/register/company`                                          | `registerCompany`        |
| GET    | `/api/v1/account/verify-email`                                              | `verifyEmail`            |
| GET    | `/api/v1/account/profile`                                                   | `getFullProfile`         |
| GET    | `/api/v1/account/dashboard`                                                 | `getDashboard`           |
| POST   | `/api/v1/exchange/create/{requestId}/{offerId}`                             | `createExchange`         |
| GET    | `/api/v1/exchange/{exchangeId}`                                             | `getExchangeDetails`     |
| PUT    | `/api/v1/exchange/{exchangeId}/accept`                                      | `acceptExchange`         |
| PUT    | `/api/v1/exchange/{exchangeId}/cancel`                                      | `cancelExchange`         |
| GET    | `/api/v1/exchange/account`                                                  | `getAccountExchanges`    |
| GET    | `/api/v1/learning-request/{requestId}`                                      | `getLearningRequestById` |
| POST   | `/api/v1/request-negotiation/respond/{requestId}`                           | `respond`                |
| GET    | `/api/v1/request-negotiation/request/{requestId}`                           | `getNegotiationHistory`  |
| GET    | `/api/v1/request-negotiation/latest/{requestId}`                            | `getLatestNegotiation`   |
| GET    | `/api/v1/request-negotiation/{requestId}/calculate-urgency/{negotiationId}` | `getNegotiationProposal` |
| PUT    | `/api/v1/request-negotiation/{negotiationId}/accept`                        | `acceptProposal`         |

### AI feature routes

| Method | Route                                                | Handler             |
| ---    | ---                                                  | ---                 |
| POST   | `/api/v1/ai/exchange/fairness/{requestId}/{offerId}` | `exchangeFairness`  |
| POST   | `/api/v1/ai/agreement/generate/{exchangeId}`         | `generateAgreement` |
| POST   | `/api/v1/ai/linkedin/get-skills`                     | `getLinkedInSkills` |
| POST   | `/api/v1/ai/linkedin/add-skills`                     | `addLinkedInSkills` |

### Other API endpoints

| Method | Route                                     | Handler                 |
| ---    | ---                                       | ---                     |
| POST   | `/api/v1/account/send-verification-email` | `sendVerificationEmail` |
| POST   | `/api/v1/session/{sessionId}/zoom`        | `createZoomMeeting`     |

### Basic CRUD endpoints

<details>
<summary>View basic CRUD routes</summary>

| Method | Route                                     | Handler  |
| ---    | ---                                       | ---      |
| GET    | `/api/v1/account/get`                     | `get`    |
| PUT    | `/api/v1/account/update/{id}`             | `update` |
| DELETE | `/api/v1/account/delete/{id}`             | `delete` |
| GET    | `/api/v1/company-profile/get`             | `get`    |
| POST   | `/api/v1/company-profile/add`             | `add`    |
| PUT    | `/api/v1/company-profile/update/{id}`     | `update` |
| DELETE | `/api/v1/company-profile/delete/{id}`     | `delete` |
| GET    | `/api/v1/exchange/get`                    | `get`    |
| POST   | `/api/v1/exchange/add`                    | `add`    |
| PUT    | `/api/v1/exchange/update/{id}`            | `update` |
| DELETE | `/api/v1/exchange/delete/{id}`            | `delete` |
| GET    | `/api/v1/individual-profile/get`          | `get`    |
| POST   | `/api/v1/individual-profile/add`          | `add`    |
| PUT    | `/api/v1/individual-profile/update/{id}`  | `update` |
| DELETE | `/api/v1/individual-profile/delete/{id}`  | `delete` |
| GET    | `/api/v1/request-negotiation/get`         | `get`    |
| PUT    | `/api/v1/request-negotiation/update/{id}` | `update` |
| DELETE | `/api/v1/request-negotiation/delete/{id}` | `delete` |

</details>
