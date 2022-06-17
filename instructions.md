# MSGNY

Imagine you work in a data science consultancy and you are tasked with submitting a project proposal to a client. Your client is a social chat messaging platform, processing billions of messages every single day. They are looking to add a misogyny detector to their live system, the implementation of which they outsource to you. The messages should be analyzed and automatically redacted in real time based on a misogyny score that your misogyny detector will produce.  

Your task is to write a project proposal for such a misogyny detection system covering the following aspects:  

## Proposed Approach
Divide the project into work streams and work packages with deliverables, and time/human resource estimates. Please focus primarily on providing detail on the data and AI strategy to answer these questions.
* What are your data needs?
* What requirements do you need to collect from the client?
* What experiments do you need to conduct?
* Are there any preprocessing considerations for the client’s data?
* What are potential AI solutions for the misogyny detector and what are the tradeoffs?
* Select the AI approach you expect to work best in practice and assume for the rest of the proposal (e.g. the system design, resource estimates) that this is going to be the solution you will be implementing.

## System Design
Sketch out high-level system architecture of your misogyny detection system, assuming it will be part of the client’s live system. You can make assumptions about how the client’s system is designed.  

You can assume that the client will provide you with the unlabelled message data. You can optionally make use of a misogyny knowledge base (KB) that your consultancy created in a previous project. The KB would contain a taxonomy of misogynistic terms and expressions, annotated with severities. It would be structured in a format that would be easily accessible for downstream processing.

This task should not take longer than 4 hours of your time and the resulting project proposal should not exceed three pages. One goal of the exercise is to get a sense of how you structure work and conduct research under real-world customer constraints. However, the main focus of the report should lie on the data science strategy. We encourage you to include scientific references and discuss AI/ML model architectures and their tradeoffs.  

Feel free to make assumptions about the technical and non-technical requirements of the client and include these assumptions in your report. Should you have any questions, please do not hesitate to reach out!
