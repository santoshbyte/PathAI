# Requirements Document: PathAI

## Introduction

PathAI is an AI-driven Virtual Concierge designed to democratize high-quality hospitality for pilgrims and tourists across India. By focusing on voice-first accessibility and real-time "Scam-Shield" intelligence, PathAI ensures travelers save money, avoid local transit scams, and navigate pilgrimage hubs with the confidence of a local.

## Glossary

- **Path_Agent**: The core AI reasoning engine powered by Amazon Bedrock (Claude 4.5 Sonnet)
- **Scam_Shield**: A proactive warning system that alerts users to common local overcharging tactics
- **Budget_Advisor**: The module responsible for providing cost-saving tips and fair-price estimates
- **Verified_Partner**: A local driver or guide who has cleared the PathAI safety audit
- **Guest_Mode**: Operational state when the system is interacting with travelers
- **Whisper_STT**: OpenAI Whisper speech-to-text engine
- **Polly_TTS**: Amazon Polly text-to-speech engine
- **Vector_Store**: Pinecone or ChromaDB database for semantic search of local intelligence
- **Valid_Query**: A voice or text input that can be parsed and understood by the system
- **Service_Request**: A guest request for hotel amenities or services
- **Standard_Fare**: The fair market price for a service based on current local rates
- **Security_Code**: A unique identifier shared between guest and vendor to verify correct vehicle/service provider
- **Scam_Alert**: A warning about known fraudulent practices in the local area
- **Fair_Price**: An AI-calculated estimate based on real-time market data to prevent overcharging
- **OTA**: Online Travel Agency (e.g., MakeMyTrip, OYO, Goibibo)
- **Best_Value**: A hotel option that optimizes price, ratings, and location proximity
- **Price_Transparency**: A detailed breakdown of all costs including base fare, taxes, and hidden fees
- **Deep_Link**: A URL that redirects users directly to a specific booking page with pre-filled search parameters

## Requirements

### Requirement 1: Multilingual Voice Interface & Budget Advisory

**User Story:** As a budget-conscious pilgrim, I want to ask for the cheapest way to travel in my native language, so I don't overspend on local services.

#### Acceptance Criteria

1. WHILE in Guest_Mode, WHEN a voice query regarding costs is received in Odia, Hindi, Bengali, or English, THE Path_Agent SHALL transcribe it using Whisper_STT
2. WHEN Whisper_STT produces a transcription, THE Path_Agent SHALL detect the language with at least 95% accuracy
3. WHEN a Valid_Query about travel costs is received, THE Path_Agent SHALL provide a "Fair_Price" estimate based on current local market rates retrieved from the Vector_Store
4. WHEN generating a cost-related response, THE Budget_Advisor SHALL include at least one "Money Saving Tip" (e.g., using public transport vs. private autos, best time to visit to avoid surge pricing)
5. WHEN the Path_Agent generates a response, THE System SHALL convert it to speech using Polly_TTS in the same language as the query
6. IF audio input quality is below 60% confidence threshold, THEN THE Path_Agent SHALL request the guest to repeat their query
7. WHEN a voice interaction completes, THE Path_Agent SHALL log the query, response, and language for analytics (with PII anonymization)

### Requirement 2: Real-time "Scam-Shield" Intelligence

**User Story:** As a first-time visitor to Puri, I want to be warned about common scams, so I don't get cheated by unofficial guides or drivers.

#### Acceptance Criteria

1. WHEN a guest queries about local transport, "Special Darshan," or paid services, THE Scam_Shield SHALL check for known scam patterns in the Vector_Store
2. WHEN a scam pattern is detected in the query context, THE Scam_Shield SHALL provide a proactive alert about known unofficial fees or common overcharging tactics
3. THE System SHALL retrieve "Scam_Alert" data from a crowd-sourced database updated by verified hotel staff and local authorities
4. WHEN Scam_Alert data is updated by authorized sources, THE System SHALL refresh Vector_Store embeddings within 5 minutes
5. WHEN a guest shares their current location and destination, THE System SHALL calculate the "Standard_Fare" for an auto-rickshaw using distance and current market rates
6. WHEN displaying a Standard_Fare, THE System SHALL show the calculation breakdown (base fare + distance charge + any applicable fees)
7. THE Scam_Shield SHALL maintain a database of common scam phrases in all supported languages (e.g., "special VIP darshan," "government approved guide fee")

### Requirement 3: Smart In-Room Service (WhatsApp-First)

**User Story:** As a hotel guest, I want to request basic amenities without a learning curve, so I feel taken care of during my stay.

#### Acceptance Criteria

1. WHEN a guest scans the in-room QR code, THE System SHALL initiate a WhatsApp conversation with a welcome message and service menu
2. WHEN a Service_Request is received via WhatsApp, THE System SHALL parse the request and extract the service type and room number
3. WHEN a Service_Request is validated, THE System SHALL assign a priority level based on urgency categories: Critical (medical help, safety issues), High (food, water), Medium (towels, toiletries), Low (extra pillows, information)
4. WHEN a Critical priority request is received, THE System SHALL immediately alert hotel staff and provide an estimated response time of under 5 minutes
5. WHEN a Service_Request is added to the queue, THE System SHALL send a confirmation message to the guest with estimated fulfillment time
6. WHEN a Service_Request is completed, THE System SHALL send a confirmation message to the guest via WhatsApp
7. THE System SHALL support rich media in WhatsApp including quick reply buttons for common requests (water, towels, housekeeping, food menu)

### Requirement 4: Simplified Vendor Marketplace

**User Story:** As a traveler, I want to book a ride with a driver I can trust at a fixed, fair price, so I can travel safely without haggling.

#### Acceptance Criteria

1. WHEN a guest requests a local service (auto-rickshaw, taxi, tour guide), THE System SHALL display only Verified_Partner options
2. WHEN displaying service options, THE System SHALL show the Standard_Fare calculated by the Budget_Advisor
3. WHEN a guest selects a Verified_Partner, THE System SHALL generate a unique Security_Code and share it with both guest and partner via WhatsApp
4. WHEN a booking is confirmed, THE System SHALL provide the guest with: driver name, vehicle number, Security_Code, and Standard_Fare
5. WHEN a service is completed, THE System SHALL request feedback from the guest within 1 hour
6. IF a Verified_Partner attempts to charge more than the quoted Standard_Fare, THE System SHALL automatically flag the partner for review
7. IF a Verified_Partner receives 3 reports of overcharging or 3 consecutive ratings below 3 stars, THEN THE System SHALL automatically suspend the partner's Verified_Partner status
8. WHEN a guest reports a pricing dispute, THE System SHALL log the incident and provide the guest with a complaint reference number

### Requirement 5: Data Security & Scalability

**User Story:** As a business owner, I want to launch PathAI in a new city (like Varanasi) in under 24 hours, so I can rapidly expand to new pilgrimage markets.

#### Acceptance Criteria

1. WHEN deploying to a new city, THE System SHALL use a "City-Agnostic" architecture where local scam data and prices are stored in separate Vector_Store namespaces
2. WHEN configuring a new city deployment, THE System SHALL require only: city name, local language preferences, base fare rates, and initial Scam_Alert data
3. WHEN a new city is added, THE System SHALL support customization of local service categories and vendor types without code changes
4. THE System SHALL comply with the Digital Personal Data Protection Act 2023 by anonymizing all guest voice logs before storage
5. WHEN storing guest interaction data, THE System SHALL encrypt all data in transit using TLS 1.3 and at rest using Amazon S3 server-side encryption
6. WHEN a guest checks out or deletes their data, THE System SHALL remove all voice recordings and personal information within 24 hours
7. THE System SHALL use AWS CloudFormation or Terraform for infrastructure-as-code deployment to enable rapid city launches

### Requirement 6: Vendor Verification and Safety Audit

**User Story:** As a hotel manager, I want to ensure only trustworthy vendors are listed, so my guests have safe and reliable service options.

#### Acceptance Criteria

1. WHEN a vendor applies for Verified_Partner status, THE System SHALL require: government ID, vehicle registration (for drivers), police clearance certificate, and two business references
2. WHEN verification documents are submitted, THE System SHALL validate document authenticity using AWS Textract
3. WHEN a vendor passes document verification, THE System SHALL conduct a background check before granting Verified_Partner status
4. WHEN a vendor is approved, THE System SHALL assign them a Verified_Partner badge valid for 12 months
5. WHEN a Verified_Partner badge expires, THE System SHALL require re-verification before allowing new bookings
6. THE System SHALL maintain an audit log of all verification activities with timestamps and reviewer information

### Requirement 7: Fair Price Database & Market Rate Updates

**User Story:** As a traveler, I want to know I'm getting fair prices based on real market rates, so I can budget accurately and avoid overpaying.

#### Acceptance Criteria

1. THE System SHALL maintain a Fair_Price database for common services (auto rides, taxi rides, tour packages, temple offerings)
2. WHEN calculating a Fair_Price, THE Budget_Advisor SHALL consider: base rate, distance, time of day, and current demand
3. THE System SHALL update Fair_Price data daily based on: Verified_Partner transaction history, guest feedback, and local authority rate cards
4. WHEN displaying a Fair_Price estimate, THE System SHALL show the price range (minimum to maximum expected) and the recommended fair price
5. THE System SHALL allow hotel staff and local authorities to submit rate updates which are reviewed before publication
6. WHEN a significant price deviation is detected (>30% from Fair_Price), THE System SHALL flag the transaction for review

### Requirement 8: Offline Fallback and Error Handling

**User Story:** As a guest, I want to receive helpful responses even when some services are unavailable, so I can still get basic assistance.

#### Acceptance Criteria

1. IF the Path_Agent (Bedrock) is unavailable, THEN THE System SHALL fall back to pre-configured template responses for common queries
2. IF Whisper_STT fails to transcribe audio, THEN THE System SHALL inform the guest and offer text-based input via WhatsApp
3. IF the Vector_Store is unreachable, THEN THE System SHALL serve cached responses for frequently asked questions and common Scam_Alerts
4. WHEN any component fails, THE System SHALL log the error with full context to Amazon CloudWatch
5. WHEN critical services are restored, THE System SHALL automatically resume normal operation without manual intervention
6. THE System SHALL send alerts to hotel staff when fallback modes are activated

### Requirement 9: WhatsApp Integration

**User Story:** As a guest, I want to interact with PathAI via WhatsApp, so I can use a familiar messaging platform without downloading new apps.

#### Acceptance Criteria

1. WHEN a guest sends a message to the PathAI WhatsApp number, THE System SHALL route it to the appropriate handler based on message content (service request, price inquiry, scam check)
2. WHEN the System sends a WhatsApp message, THE System SHALL use the WhatsApp Business API with proper message templates
3. WHEN a guest initiates contact, THE System SHALL respond within 30 seconds with a greeting and menu options
4. THE System SHALL support rich media messages including images, location sharing, and quick reply buttons
5. WHEN a conversation requires human intervention, THE System SHALL seamlessly transfer to a staff member with full conversation context
6. THE System SHALL maintain WhatsApp conversation history for 30 days for quality assurance and dispute resolution

### Requirement 10: Analytics and Reporting

**User Story:** As a hotel manager, I want to see analytics on guest queries and scam reports, so I can improve guest protection and service quality.

#### Acceptance Criteria

1. THE System SHALL generate daily reports showing: query volume by language, most common scam inquiries, and Fair_Price vs. actual transaction comparisons
2. THE System SHALL track Scam_Alert effectiveness by measuring how many guests avoided overcharging after receiving warnings
3. THE System SHALL provide weekly vendor performance reports including: booking volume, ratings, pricing compliance, and cancellation rates
4. WHEN generating reports, THE System SHALL visualize trends using charts and graphs
5. THE System SHALL allow export of analytics data in CSV and JSON formats
6. THE System SHALL provide real-time dashboards accessible via web interface with role-based access control


### Requirement 11: Smart Budget Comparison Engine

**User Story:** As a pilgrim on a strict budget, I want to see the best hotel prices across different platforms on one screen, so I can book the cheapest option quickly.

#### Acceptance Criteria

1. WHEN a guest searches for a hotel within a specific budget, THE System SHALL aggregate and compare real-time rates from at least three major OTAs (MakeMyTrip, OYO, and Goibibo)
2. WHEN displaying comparison results, THE System SHALL complete the aggregation within 10 seconds to ensure a responsive user experience
3. WHEN multiple rates are available for the same hotel, THE System SHALL highlight the "Best_Value" result based on a weighted algorithm: price (50%), guest ratings (30%), and proximity to the pilgrimage site (20%)
4. IF a direct hotel booking rate is lower than any OTA rate, THE System SHALL prioritize the direct booking link to save the guest additional commission fees
5. WHEN displaying a hotel option, THE System SHALL provide a "Price_Transparency" breakdown showing: base fare, taxes, service charges, and any hidden "resort fees" or "convenience fees"
6. THE System SHALL display the total price (all fees included) prominently to avoid confusion at checkout
7. WHEN OTA APIs are unavailable or rate-limited, THE System SHALL fall back to cached pricing data with a timestamp indicating data freshness

### Requirement 12: Personalized "Budget-Match" Recommendations

**User Story:** As a traveler, I want the AI to understand my specific needs (like "AC room for 4 people near the beach"), so I don't waste time scrolling through irrelevant options.

#### Acceptance Criteria

1. WHILE in Guest_Mode, WHEN a guest provides search criteria in natural language, THE Path_Agent SHALL use Natural Language Processing to extract constraints like: budget limit, room type, amenities (AC, WiFi), guest count, and location preferences
2. WHEN filtering hotel results, THE System SHALL apply user-specified constraints including: "kid-friendly," "walkable to temple," "near beach," "under ₹2000," "vegetarian food available," or "wheelchair accessible"
3. WHEN multiple hotels match the budget and constraints, THE Path_Agent SHALL generate a brief AI summary (max 2 sentences) explaining why a specific hotel is recommended
4. WHEN generating recommendations, THE System SHALL include comparative insights such as: "This OYO is 200m closer to the Mandir than the MMT option" or "This hotel has 15% better ratings for cleanliness"
5. THE System SHALL rank results by relevance score calculated from: constraint matching (40%), price competitiveness (30%), ratings (20%), and availability (10%)
6. WHEN a guest's budget is insufficient for available options, THE System SHALL suggest the closest alternatives with a message like: "No exact matches found. Here are options ₹300 above your budget"
7. THE Path_Agent SHALL support follow-up refinements like "show me only options with parking" without requiring the guest to restart the search

### Requirement 13: Simplified Booking Redirection

**User Story:** As a user, I want a seamless transition to the booking page, so I don't have to re-enter my dates and details.

#### Acceptance Criteria

1. WHEN a guest selects a hotel from the comparison list, THE System SHALL use Deep_Link technology to redirect the user to the specific checkout page of the chosen OTA or hotel website
2. WHEN generating a Deep_Link, THE System SHALL maintain all search parameters including: check-in date, check-out date, guest count, room type, and any special requests
3. WHEN redirecting to an OTA, THE System SHALL pre-select the exact room type and rate that was displayed in the comparison to ensure price consistency
4. IF a Deep_Link cannot be generated (due to OTA limitations), THE System SHALL copy search parameters to the clipboard and provide manual entry instructions
5. WHEN a redirection occurs, THE System SHALL open the booking page in a new browser tab/window to preserve the PathAI session
6. THE System SHALL track successful redirections and booking completions (via affiliate tracking codes where applicable) for analytics
7. WHEN a guest returns to PathAI after booking, THE System SHALL offer to save the booking confirmation details for trip planning and reminders
