# MSGNY Detection Service for ACME Co.

## Problem Definition
Social chat messaging platform ACME has faced an increase in the volume of messages sent through their platform reflecting toxic behaviour towards women. This type of behaviour, misogyny, can manifests in different forms, such as i) damning or cursing women, ii) derailing or justifying abuse and mistreatment of women, iii) discrediting or slurring of women, iv) dominance or implyig superiority of men over women, v) sexual harassment of women, vi) stereotyping and objectification of women, vii) threats of violence against women, etc.[1].  

By allowing misogyny to go unchecked, ACME faces a wide range of problems and challenges, from impacting the experience on their platform all the way to worsening the mental health of its users, and beyond. This in turn can impact ACME via higher customer churn and deterioration of its positive brand recognition in the global market.  

## Proposed Solution
As a solution, we propose embedding a misogyny detection service into ACME's live system. Our misogyny detection service analyzes streamed messages, produces a misogyny score, and automatically redacts flagged messages in real time. Misogyny detection will be implemented with multiple detection and modelling components, some of which will be enabled on ACME's server, for complex memory-and-computation demanding processes and models, while other will be enabled on ACME's clients (mobile device apps and web browser clients, if applicable) for lightweight and simple models. The latter are important for cases when users enable End-to-End encryption, and as a low-quality failsafe in case of server-side misogyny detection service malfunction or outages.

The solution on ACME's server side will consist on a service composed of a combination of light-weight models proven to work well for detecting misogyny[1][2], which classify if a message has misogynist content or not. These models need to be light weight (i.e. low memory footprint, low processing requirements, low latency) as they will be running over all messages being streamed within ACME's messaging servers. Messages which are classified with a misogyny score above a specific threshold are bypassed into a second component of the misogyny detection service, for information extraction of the specific misogynist terms or expression. Models in this component are more sophisticated and carry a higher memory and processing footprint, but safely assuming that the percentage of messages in ACME's chat messaging platform flagged as misogynist is a small fraction of all messages, these models will not impact the overall system latency, performance, and user experience. Finally, the identified misogynist terms or expressions in the flagged message are redacted in accordance with the Misogyny Policy to be designed shortly.  

## Challenges
The solution presents multiple challenges and tradeoffs, which we can help ACME navigate to reach an optimal solution. From the outset, a tradeoff must be considered between user privacy and user trust and safety. A balance must be strived between data privacy regulations and internet harmful content regulations. This balance has cascading effects on ACME's broader system design and architecture, such as the use of end-to-end encryption, which if enabled by users in the same conversation, would render the misogyny detection service ineffective for that specific conversation.  

A technical challenge is the volume of data interchanged between users, which we assume in the billions of chat messages per day; this pressures our solution to be lightweight, fast, and robust in order to safeguard ACME's live system and prevent impacting ACME's Service Level Objectives (SLO) and internal benchmarks for performance. Another technical challenge is developing algorithms that can properly handle the ambiguity in natural language, often dependant on context and culture, so as to protect users from misogyny but at the same time not impede normal and healthy conversations.

Finally, there will be ethical challenges, but we can offer our help to ACME by supporting the company define explicitly what ACME considers misogyny, design an Anti-Misogyny Policy, a governance model for reviewing and updating said policy, and a concrete process for ACME's Trust & Safety team to implement the governance model. Overcoming these ethical challenges are important as they will impact the objectives, scope, training, and implementation of the algorithms for the misogyny detection service.  

## Requirements
For implementing the misogyny detection service for ACME, we kindly request the following information.  

### Product:
* Does ACME's chat messaging platform have a group chat feature enabled?
* If so, does ACME wish to apply the misogyny detection service to group chats as well as to 1:1 chats?
* Does ACME allow for the in-chat transfer of media files, such as images and audio files?
* If so, does ACME wish to apply the misogyny detection service to misogynist content in media files?
* When misogyny has been identified in a message, how does ACME wish the message to be redacted?

### Data:
* How many languages are used by ACME users in messages?
* Which language/s does ACME wish to apply the misogyny detections service?
* What type of encryption does ACME apply to messages?
* Does ACME persist users' messages after having been successfully delivered to recipients?
* Does ACME apply some form of anonymization to persisted data from user messages?
* Do ACME users provide sex/gender in their user profile?
* Does ACME currently have a system in place for detecting fake profiles/non-human users?
* Does ACME currently have a feature for its users to provide feedback or flag if they deem a message is inappropriate?
* What is the average number of Monthly Active Users (MAU)?
* What is the average number of Daily Active Users (DAU)?
* What is the average number of active groups per day?
* What is the average number of users per active group?
* What is the average number of messages per user per day? 
* What is the average number of messages per active group per day?
* How many other users and groups does a user communicate with per day on average?
* What is the average and minimum time between messages sent by a same user?
* What is the average and minimum time for a user to respond to other users' messages?
* What is the average latency of a message from the moment it's sent by sender to the moment it's received by recipient?
* Do you track user sessions, and if so, how do you define what constitutes a user session?

### System:
* Does ACME currently have the architecture to integrate an analytics streaming service on their chat messages pipeline?

## Assumptions
To proceed with our proposal, we will at this time postulate the following assumptions for guiding our design and plan:  
* ACME does offer group chats and we shall apply the misogyny detection service to them.
* ACME does offer in-chat media files, but at this stage they will be left out of scope. We can expand the scope to include them at a later iteration, with the addition of an Automatic Speech Recognition (ARS) service for audio files, and an Optical Character Recognition (OCR) service for text files.
* ACME users send messages in dozens of languages, but at this iteration the service will initially be for messages in English language.
* ACME does not offer End-to-End encryption by default, but users can enable it manually. 
* Non End-to-End data is however encrypted in traffic, and ACME has access to encryption keys for decrypting messages, and can provide us with unencrypted message data.
* ACME persists messages in a NoSQL database, even after delivered successfully to their corresponding recipients.
* ACME does not have sex/gender information on their users we can use to better identify targets of misogynist content.
* Redaction of a term or expression in a message flagged as misogynist will be done by masking the span of the term or expression with consecutive pound (#) symbols.

## Data Preprocessing
Our misogyny detection service can implement the following data preprocessing components. The final set of components applied to ACME's live system will depend on the outcome of the planned experiments, given i) the results on latency from applying the individual component vis-a-vis SLO defined by ACME, and ii) the accuracy gain each component provides given thh complexity and distribution of ACME's messages data:
* Language detection: For this iteration, our service will filter and process only messages in English language.
* Anonymization: Remove any personal identifiable information (PII) in message metadata cached or temporarily persisted in the misogyny detection service environment.
* Message thread windows: Identify and tag sequences of messages within a given window of time or context, in order to identify covert or latent misogynist content across multiple messages in a 1:1 chat or group conversation.
* Non-alphabetic symbol processing: Extract, translate (where possible), and tag to messages' metadata definitions of non-alphabetic content using emoji and symbol dictionaries, for identifying misogynist content not expressed alphabetically.
* Acronym expansion: Use formal and vernacular acronym dictionaries to tag messages' metadata with potential acronym expansions.
* Typos handling: To identify mistyped misogynist words or phrases that may not be flagged otherwise. Alternatives for this component include using i) lists of most common typos in English, ii) Levenshtein automata, iii) character embeddings, etc.
* Sentence parsing: Since messages data can significantly lack punctuation symbols, heuristics and models will be applied to identify and parse messages composed of multiple sentences.
* Word/phrase tokenization: Splitting strings of characters into relevant words and phrases (multi-word expressions). For the latter, methods such as npmi and similar word co-occurrence extraction methods can be efficiently applied.
* Normalization: In order to improve data post-processing and modelling stages, normalization can help reduce the vocabulary size of corpora, and identify synonyms and similar misogynist terms that may require flagging and redaction. We can implement normalization via different methods, depending on the experiment and type of modelling being explored. Such methods include i) Knowledge Base (KB)-driven synonym contraction via controlled vocabularies and thesauri, ii) stemming, iii) lemmatization, etc.

## Modelling
Our misogyny detection service relies on a battery of Natural Language Processing (NLP) methods and models, ranging from low-memory and low-processing demanding ones, to state-of-the-art high-memory and high-processing demanding ones.  

### First Order Models
The initial set of NLP methods and models implemented for our misogyny detection service consists of the following components, used for extracting features and text representation:
* Misogyny Knowledge Base: 
    * Our proprietary KB provides us with a taxonomy of misogynistic terms and expressions, annotated with severities.
* String matching models: 
    * Fed by terms and expressions from our proprietary KB, our simplest component is a string-matching model that can identify and flag a misogynist term or expression in an unlabelled message.
    * Terms identified with string matching models tend to have a high precision, especially as we move away from single-word terms, and towards matching expressions with multiple wordsHowever, the tradeoff is that they have a low recall, as word inflections, variations, and typos are easily missed. 
    * String-matching models tend to produce high rates of true positives, except when faced with homonyms or contextual expressions, as they can't distinguish discourse context or words overloaded with multiple meanings.
    * When a misogynist term is identified in a message, information associated to the term in the KB can be added to the message's metadata as semantic enrichments. Examples of these could be tags for what type of misogynist category the term is associated to in the KB (threat of violence, slurs, etc.).  
    * String matching model also provides the location of a misogynist terms in a text, which can also be added to the message's metadata.
    * The features provided by a string matching model, i.e. actual term instance, term associated labels, and term instance location in text, provide the building blocks to Second Order Models below (i.e. supervised learning), such as Conditional Random Fields (CRF) and other, enabling tasks such as Named Entity Recognition (NER).
* Regex pattern extraction:
    * Similar to the string matching models above, Regular Expression (regex) models allow the use of textual patterns for matching sequences of strings in a message.
    * Regex models improve on the recall achieved by string matching models as they can cover more alternatives and variations of a term, but it comes with the tradeoff of reducing precision by often matching false positives (i.e. terms that match the regex pattern, but do not represent a misogynist term in this scenario).
    * As per string matching models, regex models can also provide location of a term within a sentence, and labels associated to a specific pattern (e.g. countries, emotions, proper nouns, etc.), also providing useful labels for downstream supervised learning models.
* Embedding representation:
    * Once messages have been parsed and tokenized, and features extracted from them, different forms of representing the textual data can be implemented, depending on the downstream model that will consume it. For our purposes, the representation models we will experiment with are Bag-of-Words (BOW), Term Frequency-Inverse Document Frequency (TF-IDF), and word embeddings in the case of deep learning techniques.

### Supervised Machine Learning
With the features leveraged from string-matching and regex models, we train and test different supervised machine learning models to experiment i) their accuracy for classifying messages as misogynist, and ii) their performance for handling a stream of billions of messages per day. We preselect a set of machine learning models to test with, which have shown good results in previous work on misogyny detection[1][2]. 

#### Binary Classification:
For the initial component of the misogyny detection service which flags if a message contains misogynist content or not, we implement the following ensemble of two models. The key aspects of these models are that they offer relatively high-speed calculations, don't rely on excessive data for producing accurate results, and are relatively quick to train and retrain:
* Naive Bayes Model:
    * In spite of the advances in machine learning, the Naive Bayes remains as a simple, fast, accurate, and reliable algorithm for simple text classification, among other tasks.
    * The Naive Bayes model helps us identify the probability of a message being flagged as misogynist, given what we know of the type of features (e.g. terms or expressions) that often appear in messages which indeed contain misogynist terms or expressions.
* Logistic Regression:
    * Another traditional statistical model, commonly used for text classification, which uses a logistical function to classify if a given message belongs to one of two categories; in our case, if a message does not contain misogynist content or if it does. 
    * As the Naive Bayes, it's easy to train, easy to implement in production, and it's accurate as long as the input words in the message are assumed to be linearly related to the fact of if a message is misogynist or not.
    * A downside of Logistic Regression and Naive Bayes is when classifying messages that contain covert or subjective misogynist content, such as innuendos and sarcasm.
* An ensemble model from combining the results of the Naive Bayes and the results of Logistic Regression is constructed to pool the calculations from both models. This ensemble model is then deployed straight into the stream of messages within ACME's messaging servers, either on the back of an analytics streaming process such Spark Streaming, or simplified bespoke implementation if ACME's messaging servers are replicated and distributed.

#### Multi-Class Classification:
For the second component of the misogyny detection service, messages which have been flagged as likely to contain misogynist content are processed for information extraction. The objective is to identify the exact misogynist term or expression in the message, and the location of it the text. This information will then be used by the subsequent component in the service to redact the message excluding the misogynist content. The models used in this stage are:
* Conditional Random Field Models:
    * CRF are statistical modelling methods applied in pattern recognition that can take into account the context of feature (i.e. token or sequence of tokens).
    * CRF have traditionally performed well in named entity recognition tasks, particularly when off-the-shelve models are unavailable, this having to train one's own bespoke domain-specific NER models.
    * CRF models will enable better information extraction activities by labelling specific terms or expressions in a message as a special entity, either a Named Entity, or a bespoke domain-specific entity of interest for the task at hand. In our case, we would train CRF models on misogyny-specific aspects, such as entities representing a woman (e.g. based on person's names, or other woman-specific features).
    * Tradeoffs of CRF models are that they require significant amounts of labelled data to train, take a long time to train or fine-tune, and can become brittle with data drift.
* Bert Fine-tuned for Misogyny Domain:
    * Bert represents a family of "Bidirectional Encoder Representations from Transformers" models, which have performed very well in many NLP tasks, including multi-class classification of text. 
    * To enhance the downstream message redaction component of the misogyny detection service, we will use Bert to identify if a message flagged as misogynist falls into any of the following categories:
        * Damning or cursing women
        * Derailing or justifying abuse and mistreatment of women
        * discrediting or slurring of women
        * dominance or implying superiority of men over women
        * sexual harassment of women
        * stereotyping and objectification of women
        * threats of violence against women
        * none of the above
    * Understanding to which class of misogyny a flagged message belongs to can facilitate how to effectively redact a message, minimizing the risks and challenges mentioned in the Challenges section above.
    * If the Bert model classifies a flagged message with "none of the above", we don't pass the message to the redaction stage and consider it a false positive from the binary classification ensemble model.

## Experiments
In order to train high quality models and set up a misogyny detection service that integrates well with ACME's live system, we will perform the following experiments to gauge the effectiveness and performance of the service:
* Compare misogynist messages in 1:1 conversations vs messages in group chats. 
    * Our hypothesis is that group chats generate more misogynist behavior.
    * If true, this has implications on the evolution of the architecture of the misogyny detection service, as where in the pipeline a message is checked for misogyny and redacted can have software design impacts, if the messages to the group recipients are routed in a more distributed fashion at the beginning of the messages pipeline.
* Evaluate binary classification models independently and vs. an ensemble of them together.
    * Our hypothesis is that the ensemble will provide more accurate results overall, while with some minimal overhead in terms of latency when compared to the Naive Bayes only.
* Compare our binary and multi-class classification models against academic benchmarks.
    * Our hypothesis is that our models will under-perform academic benchmarks when predicting on the benchmark's official data. That is, Twitter data, which has a significantly different semantic and behavioral distribution than ACME's messages.
    * We believe we might see a higher similarity with academic benchmarks when it comes to group chat messages. If this is true, we might consider incorporating some of the results and recommendations from academic studies on misogyny detection into future iterations of our service for ACME.
* Beta test with cohort of random users to flag when unredacted misogynistic messages filter through (i.e. false negatives).
    * Our assumption is that there will be a relatively high rate of false negatives to misogynist content that is not explicit, but rather covert, sarcastic, or subconscious.
    * These experiments require a feedback mechanism to allow users to flag said messages. This could be a feature in the chat if it already exists, or an analog method via customer service.
* A/B testing the redaction of messages containing misogynist content.
    * Our assumption is that redacting messages will prompt many receiving users to actually engage further in misogynist conversations, partially curious and partially angry to know what was said to them by the sender.

## High-level Architecture Design

![high_level_architecture_design](/diagrams/high_level_architecture_design_chat_platform.png)
  
#### Main Behavior:
1. User A is routed via a load balancer to a gateway where a websocket connection is established.
2. Websocket forwards connection to a messages service.
3. Messages service queries database for undelivered messages to user A (who recently came online).
4. Messages service delivers undelivered messages to user A.
5. Messages service receives messages from different users directed to User A.
6. Messages service places received messages in an in-memory message queue
7. Messages service persists messages in database.
7. Messages service processes messages in message queue and routes to User A.
8. User A types a message for User B.
9. Message is forwarded to messages service.
10. Messages service queries websocket manager for User B's messages service.
11. Messages service sends message to User B's messages service.
12. User B's messages service places received message in User B's message queue and database.
13. User B's messages service queries websocket manager for User B's websocket route.
14. If User B is currently offline and remains online for longer, User B's messages service persists any remaining state information to database, and shuts down thread to release resource.
15. User B comes online and is routed via a load balancer to a gateway where a websocket connection is established...

## Low-level System Design
![low_level_design_misogyny_detection_service](/diagrams/low_level_design_misogyny_detection_service.png)

#### Main Behavior:
1. Messages service receives message from User A to User B.
2. Messages service parses the text from the message and feeds it to the binary classification component.
3. If message is classified as not misogynist, messages service places message in queue.
4. If classified as misogynist, messages service feeds the text to the multi-class classification component.
5. If message is classified with misogyny type `none`, messages service places the message in queue.
6. If message is classified with an actual misogyny type, messages service feeds the text to the message redaction component.
7. Messages service places redacted message in queue.

## Work Streams, Work Packages, Resourcing

#### 1. Prototyping
    1. Train and test data acquisition and ingestion. | Data Engineer | 1-2 weeks
    2. Extract controlled vocabularies and taxonomies from Knowledge Base. | Knowledge Engineer | 1-2 weeks
    3. Generate semantic enrichment and extracting features from training data. | Knowledge Engineer | 2-4 weeks
        * String matching models
        * Regex models
    4. Train and testing binary classification models. | NLP Engineer | 2-4 weeks
    5. Ensemble and testing binary classification models. | NLP Engineer | 1-2 weeks
    6. Train and testing multi-class classification models. | NLP Engineer | 8-12 weeks
    7. Run experiments. | Data Scientist | 8-12 weeks
    8. Fine-tune of models. | NLP Engineer | 2-4 weeks
    
    Total Resources:
    * Data Engineers:        1   |   2 weeks 
    * Knowledge Engineers:   1   |   6 weeks  
    * NLP Engineers:         2   |   22 weeks
    * Data Scientists:       1   |   12 weeks

#### 2. Productionization
    1. Define training data version control system. | Data Engineer | 1-2 weeks
    2. Define production model version control system. | MLOps Engineer | 1-2 weeks
    3. Build messages service <-> misogyny detection service interface | Backend Engineer | 2-4 weeks
    4. Inject telemetry framework into misogyny detection service. | Backend Engineer | 2-4 weeks
    5. Containerize misogyny detection service. | MLOps Engineer | 2-4 weeks
    6. Test containerized misogyny detection service in client playground environment. | MLOps Engineer | 2-4 weeks
    
    Total Resources:
    * Data Engineers:        1   |   2 weeks  
    * Backend Engineers:     1   |   8 weeks
    * MLOps Engineer:        1   |   10 weeks

#### 3. Deployment
    1. Integrate misogyny detection service telemetry with monitoring system. | Backend Engineer | 2-4 weeks
    2. Build dashboards and alerting system. | Backend Engineer | 1-2 weeks
    3. Deploy misogyny detection service in canary instance of a messages service. | DevOps Engineer | 2-4 weeks
    4. Test misogyny detection service in canary instance with minimal traffic. | DevOps Engineer | 1-2 weeks
    5. Evaluate performance of misogyny detection service in canary instance and UX. | DevOps Engineer | 1-2 weeks
    6. Increment canary instances of misogyny detection service via canary release. | DevOps Engineer | 1-2 weeks

    Total Resources:
    * Backend Engineers:     1   |   6 weeks
    * DevOps Engineer:       1   |   10 weeks

#### 4. Management
    1. Define on-point support engineer. | DevOps Engineer | Ongoing @ 25% capacity
    2. Measure model drift. | Data Scientist | 1 week per month
    3. Retrain and update models. | NLP Engineer | 2-4 weeks per quarter

    Total Resources:
    * DevOps Engineers:      1   |   13 weeks per year
    * Data Scientists:       1   |   12 weeks per year
    * NLP Engineers:         2   |   16 weeks per year

## Conclusion
We look forward to working together with ACME to help fight toxic behavior on social media platforms. Please don't hesitate to contact us if you have any questions or comments with regards to our proposal, our solution, or our company.


## References
* [1]: Al-antari, Mugahed A. & Muaad, Abdullah Y & Davanagere, Hanumanthappa & Jv, Bibal Benifa & Chola, Channabasava. (2021). AI-based Misogyny Detection from Arabic Levantine Twitter Tweets. 10.3390/IOCA2021-10880.
* [2]: Cardiff, J. & Shushkevich, E. (2018). Misogyny detection and classification in english tweets:the experience of the ITT team. In Proceedings of the Sixth Evaluation Campaign of Natural Language
Processing and Speech Tools for Italian. Final Workshop (EVALITA 2018) co-located with the Fifth Italian Conference on Computational Linguistics (CLiC-it 2018) Turin, Italy, December 12-13. 
