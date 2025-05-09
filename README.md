Welcome to the developer’s guide for EVA. Below, is an outline of this guide.

**Outline**

  1. Introduction
  2. Project Architecture
  3. Development Environment Setup
  4. Smart Contract Development
  5. NodeJS Server Development
  6. Deployment
  7. Security Best Practices
  8. Future Roadmap and Updates
  9. Resources and Further Reading



**1. Introduction**

Welcome to the developer's guide for EVA! This innovative platform is designed to revolutionize the way students rate and review their professors using blockchain technology. By leveraging the transparency, security, and immutability of blockchain, our platform ensures that reviews are trustworthy, accurate, and accessible.

Our primary goal is to empower authorized users (i.e., individuals who can sign in with their university email) to leave reviews for professors within their respective institutions. Each university maintains a list of professors, which is stored in our database. Each professor is associated with a unique blockchain public key and has reviews from their students. Unlike traditional platforms, our system stores reviews directly on the blockchain rather than a centralized database. This approach enhances the security and integrity of the reviews while promoting transparency.

To retrieve reviews, our server searches the blockchain for events emitted by our smart contract. This ensures that only legitimate reviews are displayed and reduces the chances of manipulation or tampering. Additionally, before publishing a review, our NodeJS server employs the ChatGPT API to analyze the content for hate speech. This feature promotes a safe and respectful environment for users and helps maintain the quality of the reviews.

In this guide, we will walk you through the different aspects of the project, providing insights into its architecture, development, and deployment processes. By the end of the guide, you should have a solid understanding of the project's inner workings and be able to contribute, maintain, and extend the platform as needed. So, let's dive into the world of Blockchain-based Rate My Professor and explore its potential together!



**2. Project Architecture**

The project's architecture is organized into modular components, ensuring a clear separation of concerns and promoting maintainability. The primary components include:
  
  1. server.js: The main script for the server. It handles incoming API requests, such as creating new reviews and retrieving existing reviews, and manages the interactions between the other components of the system.
  
  2. contract.js: This script is responsible for interacting with the smart contract on the blockchain. It contains functions for adding and retrieving reviews, as well as any other operations that involve the smart contract.

  4. ApiServices/index.js: This script communicates with the OpenAI API to check if submitted reviews contain hate speech. By integrating with the OpenAI API, the platform ensures that reviews maintain a respectful tone and adhere to community guidelines.
    
  5. database.js: This script contains the SQLite functions that manage the database. The database stores information about supported universities, lists of professors within each university, and other supporting functions.
The project's architecture ensures that each component focuses on a specific task, making it easier to maintain, debug, and extend the system.



**3. Development Environment Setup**

Before diving into the development of the project, it is essential to set up the development environment properly. Follow these steps to get started:
  1. Install Node.js: The project is built on Node.js, so ensure that you have the latest LTS version installed on your machine. You can download Node.js from the official website: https://nodejs.org/
  
  2. Clone the repository: Clone the project repository to your local machine using your preferred Git client or by running the following command in your terminal:
    a. git clone <repository-url>

  3. Install dependencies: Navigate to the project's root directory in your terminal and run the following command to install the required dependencies:
     a. npm install

  4. Configure environment variables: Create a .env file in the project's root directory and add the necessary environment variables, such as the Alchemy API Key, private key, and Google Client ID. Make sure to keep this file secure, as it contains sensitive information.

  5. Run the server: Start the server by running the following command in the project's root directory:
     a. node server.js

  6. Verify the server: To verify that the server is running correctly, open your browser and navigate to http://localhost:3001. You should see a message indicating that the server is running. With the development environment set up, you are now ready to dive into the project's code and start contributing to the Blockchain-based Rate My Professor platform!



**4. Smart Contract Development**

**Language**

The smart contract for this project is written in Solidity.

**ProfReview.sol Script**

The **ProfReview.sol** script contains the core smart contract implementation for the
project. It has the following primary functions:

1. **addReview**: This function allows the owner of the contract to add a new review for a specific professor. It takes the following parameters:
2. 
  - **_profID**: A unique identifier for the professor.
  - **_newReview**: The text of the review.
  - **_newRating**: The rating given by the reviewer (e.g., on a scale of 1 to 5).

The smart contract also includes an event called UpdateReviews which is emitted when a new review is added.

**Access Control**

The **addReview** function is protected by the **checkSender** modifier, which ensures that only the contract owner can call this function. This is achieved by checking if **msg.sender** is equal to the owner address. If the check fails, the transaction is reverted with an error message.

**Deploy.js Script**

The **deploy.js** script is responsible for deploying the **ProfReview** smart contract. The script connects to the Goerli testnet using Alchemy API and deploys the contract using the Hardhat framework. Once the contract is deployed, its address is logged to the console.

**Contract.js Script**

The **contract.js** script contains the necessary functions to interact with the deployed **ProfReview** smart contract. It includes the following functions:
  
  1. addReview: This function is used to add a review to the blockchain. It calls the addReview function in the smart contract and passes the required parameters (profID, review, and rating).
  
  2. getReviews: This function retrieves the specific reviews for a professor. It uses the getPastEvents method to filter and collect all the UpdateReviews events for the given professor ID. It then calculates the average rating and returns the list of reviews along with the average rating.

**Deployment**

The smart contract is deployed on the Goerli testnet using the Hardhat framework and Alchemy API. The deployment process is handled by the **deploy.js** script, which compiles the smart contract and deploys it to the testnet.



**5. NodeJS Server Development**

In this section, we'll discuss the development of the NodeJS server for the "Blockchain based Rate My Professor" project. The server is built using the Express framework and handles user authentication, interaction with the smart contract, and database queries.

**API Endpoints**
The server exposes the following main API endpoints:

  1. **POST /api/addReview**: Receives a request to create a new review.

  2. **GET /api/getAllProfessors**: Fetches a list of all professors from the SQLite database.

  3. **GET /api/getUniversities**: Retrieves a list of universities from the SQLite database.

  4. **GET /api/getReviews/:id**: Receives a request to get all reviews for a professor with the given id.

**Middleware and Third-Party Libraries**

The server uses the following middleware and third-party libraries:
  - **express**: The core framework for the server.
  - **cors**: A middleware to handle Cross-Origin Resource Sharing (CORS).
  - **google-auth-library**: A library for verifying Google user authentication tokens.

**Error Handling and Logging**

Error handling in the server is achieved using **try-catch** blocks. When an error occurs, it is logged to the console, and an appropriate response is sent back to the client with an error message and an HTTP status code indicating the error.

**Performance and Security Considerations**

User authentication is handled using Google authentication tokens, ensuring that only users with valid university email addresses can leave reviews. Additionally, the server checks for hate speech in reviews using the ChatGPT API before processing and adding them to the blockchain. These measures help improve the security and quality of the reviews.

**User Authentication**

User authentication is performed using the Google OAuth2 library. When a user submits a review, the server verifies their Google authentication token to ensure they have a valid university email address. If the token is invalid or the email does not have the appropriate suffix, the server returns an error.

**Server Interactions**

The server connects to and interacts with the following components:
  1. **Smart Contract**: The server communicates with the smart contract using the **contract.js** script. This script exports functions to add reviews to the blockchain and fetch reviews for a given professor.
  2. **SQLite Database**: The server queries the SQLite database using the **database.js** script, which exports functions to fetch professors and universities.
  3. The server uses these functions to handle requests to the /api/getAllProfessors and /api/getUniversities endpoints.
  4. **ChatGPT API**: The server interacts with the ChatGPT API through the **ApiServices/index.js** script to check if a review contains hate speech.

Overall, the NodeJS server serves as a bridge between the frontend, the smart contract, the SQLite database, and the ChatGPT API, providing a seamless experience for users to rate their professors.



**6. Deployment**

Deploying the Smart Contract to Mainnet

Deploying the smart contract to the Ethereum Mainnet is a similar process to the deploy.js script used for local testing. However, instead of using a test network, you will need to configure the deployment script to connect to the Ethereum Mainnet. You should also ensure that you have enough Ether in your deployment wallet to cover the gas fees for the deployment.

**Setting up and Managing Production Nodes**

To set up and manage production nodes, you will need to:

  1. Choose a hosting provider, such as AWS, Azure, or GCP.

  2. Configure the server with the necessary resources, such as RAM, storage, and processing power.

  3. Install the required software, such as Node.js and any required dependencies.

  4. Set up monitoring and alerting tools to track the health and performance of your nodes.

  5. Configure automatic backups and updates to keep your nodes secure and up-to- date.

**Configuring Network Parameters and Security Best Practices**

To ensure the security and performance of your network, you should:
  1. Configure a firewall to limit incoming and outgoing traffic to the necessary ports and protocols.

  2. Use SSL/TLS certificates to encrypt communication between the server and clients.

  3. Use secure authentication methods, such as OAuth2, to protect user accounts and data.

  4. Regularly review and update your server and application configurations to stay compliant with security best practices.



**7. Security Best Practices**

**Private Key Management**

Proper private key management is critical for the security of your project. You should:

  1. Store private keys securely, using hardware wallets or secure key management systems.

  2. Avoid hardcoding private keys in your application or scripts.

  3. Regularly rotate private keys and revoke access to compromised keys.

**Authentication and Authorization Mechanisms**

Implement strong authentication and authorization mechanisms to protect user data and access to sensitive functionality. You should:

  1. Use secure authentication protocols, such as OAuth2 or SAML, for user authentication.

  2. Implement role-based access control (RBAC) to limit user access to specific functionalities.

  3. Regularly review and update user access rights to maintain the principle of least privilege.

**Smart Contract and Server Security Considerations**

To ensure the security of your smart contract and server, you should:

  1. Conduct regular code reviews and security audits.

  2. Implement strict input validation and output sanitization to prevent injection attacks.

  3. Use secure coding practices and follow established patterns to minimize the potential for vulnerabilities.



**8. Future Roadmap and Updates**

**Planned Features and Improvements** 

The project's future roadmap includes:

  1. Implementing additional filters and search functionality for professors and reviews.

  2. Supporting more universities and expanding the database of professors.

  3. Incorporating user feedback to improve the platform's user experience.

**Contribution Guidelines for External Developers**

External developers are encouraged to contribute to the project by:

  1. Reporting bugs and security vulnerabilities.

  2. Submitting pull requests for new features and improvements.

  3. Participating in discussions and providing feedback on the project's direction.

**Keeping up to Date with Project Updates**

Stay informed about project updates by:

  1. Following the project's GitHub repository and checking the release notes for new versions.

  2. Subscribing to the project's mailing list or newsletter.

  3. Joining the project's community on social media platforms or forums.

**Resources and Further Reading**

References to External Libraries and Tools Used in the Project

  1. Express.js

  2. SQLite

  3. web3.js

  4. Ethers.js

  5. Google OAuth2Client

**Contact Information for the Development Team**

To get in touch with the development team, you can:

1. Reach out via the project's GitHub repository by submitting issues or pull requests.
  
3. Contact the team via email at nuriyev@bc.edu, anderagh@bc.edu, malx@bc.edu, licjn@bc.edu, and duffybn@bc.edu
   
By following the guidelines and information provided in these sections, you can better understand the project's architecture, deployment, security, future roadmap, and resources for further learning. This will enable you to contribute to the project, stay up to date with its progress, and utilize it effectively.
