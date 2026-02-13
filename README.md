# PathAI_

> **v1.0.0-alpha** | **Build Status:** Stable | **Region:** IND-East 

PathAI is a decentralized trust protocol for unorganized hospitality markets. It automates scam detection, price benchmarking, and cross-platform inventory aggregation using a voice-first, agentic architecture.
**Empowering the next billion pilgrims with voice-first accessibility and local scam-prevention intelligence.**

## [The Mission]
Every year, millions of pilgrims visiting hubs like **Puri**, Varanasi, and Ayodhya face language barriers and "tourist taxes" (scams). **PathAI** is a hyper-local AI assistant designed by locals to protect travelers from overcharging while providing a seamless, budget-friendly booking experience.

##  [Key Features]
* Scam-Shield Intelligence:** Proactive alerts on known local overcharging tactics and "unofficial fees."
*  Voice-First Multilingual Support:** High-accuracy voice reasoning in Odia, Hindi, Bengali, and English.
*  Budget Comparison Engine:** Real-time rate comparisons across MMT, OYO, and local verified guest houses.
*  Verified Marketplace:** Direct connection to safety-audited local drivers with fixed "Fair-Price" quotes.
*  <24hr City Launch:** A city-agnostic architecture ready for rapid deployment to any pilgrimage site in India.

##  [Tech Stack (AWS Ecosystem)]
* **LLM Reasoning:** Amazon Bedrock (Claude 3.5 Sonnet)
* **Orchestration:** LangChain / AWS Step Functions
* **Voice Engine:** OpenAI Whisper (STT) + Amazon Polly (TTS)
* **Database:** RAG-based Vector Store (Pinecone/ChromaDB) for real-time temple & city intel

##  [Founder's Story]
Based in **Puri, Odisha**, I’ve witnessed firsthand the struggle of pilgrims navigating our city. PathAI isn't just a technical project; it's a solution to a cultural and operational problem I see every day at the Jagannath Temple.

---

### [ SYSTEM_ARCHITECTURE ]

```mermaid
graph LR
    User((User Voice)) -->|Stream| Gateway[Edge Gateway]
    Gateway -->|Buffer| A[Whisper-v3]
    
    subgraph Core_Logic [AWS Bedrock Runtime]
        A -->|Text| B{Intent Router}
        B -->|Risk Analysis| C[Scam_Shield]
        B -->|Market Data| D[Budget_Engine]
        C -->|RAG Fetch| E[(Pinecone Vector DB)]
        D -->|API Call| F[OTA Aggregator]
    end
    
    C & D -->|JSON| G[Response Synth]
    G -->|Audio| H[Polly Neural]
    H --> User
