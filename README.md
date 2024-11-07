# Automated Conversational Chatbot for Form Filling

This project is an interactive, automated chatbot designed to streamline form-filling processes by generating dynamic, conversational questions based on specific use cases. Built with Vue.js, this chatbot can be integrated with various databases, enhancing user experience through a guided, conversational approach.

## Project Overview

This chatbot divides the form-filling process into multiple use cases, allowing for customized, real-time metadata generation tailored to each client's requirements. Each question is dynamically generated, validated, and then presented to the user through a Vue.js widget. User responses are stored in a data model and can be integrated with any database across platforms.

## Key Features

- **Dynamic Question Generation**: Generates questions in real-time based on the context and metadata of each use case.
- **Flexible Use Cases**: Supports various use cases, such as:
  - Normal forms
  - Appointment booking
  - Quizzes or IQ tests
  - Polling and surveys
  - Resume generation
- **Conversational Flow**: Utilizes a Vue.js widget to guide users through each question in a conversational manner.
- **Data Storage and Validation**: Validates user responses and stores them in a database model for easy access and retrieval.

## Project Flow

1. **Use Case Selection**: Identifies the specific form type based on use case metadata.
2. **Dynamic Metadata Processing**: Custom metadata is generated for each use case and passed through a conversational question generation model.
3. **Question Generation and Validation**: The generated question is validated and presented to the user via a Vue.js widget.
4. **User Interaction and Data Capture**: Users answer the questions, with responses being validated and stored in a model.
5. **Database Integration**: The stored responses can be integrated into any chosen database for further use.

## Flowchart

![Project Flowchart](./path_to_images/Flowchart.png)

## Screenshots

### Screenshot 1: Date Selection View
![Screenshot 1](./path_to_images/1.png)

### Screenshot 2: Dynamic Question Generation Based on Metadata
![Screenshot 2](./path_to_images/2.png)

### Screenshot 3: Recalling different parts of the chatbot
![Screenshot 3](./path_to_images/3.png)

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) (v14 or higher recommended)
- [Vue CLI](https://cli.vuejs.org/) for running the Vue.js app

### Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/your-username/your-repository.git
   cd your-repository
