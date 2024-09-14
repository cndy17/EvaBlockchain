Developer Guide
Welcome to the developer’s guide for EVA. Below is an outline of this guide.
Outline
1. Introduction
2. Project Architecture
3. Development Environment Setup
4. Smart Contract Development
5. NodeJS Server Development
6. Deployment
7. Security Best Practices
8. Front-End Architecture
9. Local Website Setup
10. Future Roadmap and Updates
11. Resources and Further Reading

 1. Introduction
Welcome to the developer's guide for EVA! This innovative platform is designed to revolutionize the way students rate and review their professors using blockchain technology. By leveraging the transparency, security, and immutability of blockchain, our platform ensures that reviews are trustworthy, accurate, and accessible.
Our primary goal is to empower authorized users (i.e., individuals who can sign in with their university email) to leave reviews for professors within their respective institutions. Each university maintains a list of professors, which is stored in our database. Each professor is associated with a unique blockchain public key and has reviews from their students. Unlike traditional platforms, our system stores reviews directly on the blockchain rather than a centralized database. This approach enhances the security and integrity of the reviews while promoting transparency.
To retrieve reviews, our server searches the blockchain for events emitted by our smart contract. This ensures that only legitimate reviews are displayed and reduces the chances of manipulation or tampering. Additionally, before publishing a review, our NodeJS server employs the ChatGPT API to analyze the content for hate speech. This feature promotes a safe and respectful environment for users and helps maintain the quality of the reviews.
In this guide, we will walk you through the different aspects of the project, providing insights into its architecture, development, and deployment processes. By the end of the guide, you should have a solid understanding of the project's inner workings and be able to contribute, maintain, and extend the platform as needed. So, let's dive into the world of Blockchain-based Rate My Professor and explore its potential together!
2. Project Architecture

 The project's architecture is organized into modular components, ensuring a clear separation of concerns and promoting maintainability. The primary components include:
1. server.js: The main script for the server. It handles incoming API requests, such as creating new reviews and retrieving existing reviews, and manages the interactions between the other components of the system.
2. contract.js: This script is responsible for interacting with the smart contract on the blockchain. It contains functions for adding and retrieving reviews, as well as any other operations that involve the smart contract.
3. ApiServices/index.js: This script communicates with the OpenAI API to check if submitted reviews contain hate speech. By integrating with the OpenAI API, the platform ensures that reviews maintain a respectful tone and adhere to community guidelines.
4. database.js: This script contains the SQLite functions that manage the database. The database stores information about supported universities, lists of professors within each university, and other supporting functions.
The project's architecture ensures that each component focuses on a specific task, making it easier to maintain, debug, and extend the system.
3. Development Environment Setup
Before diving into the project's development, it is essential to set up the development environment properly. Follow these steps to get started:
1. Install Node.js: The project is built on Node.js, so ensure that you have the latest LTS version installed on your machine. You can download Node.js from the official website: https://nodejs.org/
2. Clone the repository: Clone the project repository to your local machine using your preferred Git client or by running the following command in your terminal:

 
 1. git clone <repository-url>
3. Install dependencies: Navigate to the project's root directory in your terminal and run the
following command to install the required dependencies: 1. npm install
4. Configure environment variables: Create a .env file in the project's root directory and add the necessary environment variables, such as the Alchemy API Key, private key, and Google Client ID. Make sure to keep this file secure, as it contains sensitive information.
5. Run the server: Start the server by running the following command in the project's root directory:
1. node server.js
6. Verify the server: To verify that the server is running correctly, open your browser and
navigate to http://localhost:3001. You should see a message indicating that the server is running.
With the development environment set up, you are now ready to dive into the project's code and start contributing to the Blockchain-based Rate My Professor platform!
4. Smart Contract Development
Language
The smart contract for this project is written in Solidity.
ProfReview.sol Script

   
 The ProfReview.sol script contains the core smart contract implementation for the project. It has the following primary functions:
1. addReview: This function allows the owner of the contract to add a new review for a specific professor by emitting an event onto the smart contract. It takes the following parameters:
○ _profID: A unique identifier for the professor.
○ _newReview: The text of the review.
○ _newRating: The rating given by the reviewer (e.g., on a scale of 1 to 5).
The smart contract also includes an event called UpdateReviews which is emitted when a new review is added.
Access Control
The addReview function is protected by the checkSender modifier, which ensures that only the contract owner can call this function. This is achieved by checking if msg.sender is equal to the owner address. If the check fails, the transaction is reverted with an error message.
Deploy.js Script
The deploy.js script is responsible for deploying the ProfReview smart contract. The script connects to the Goerli testnet using Alchemy API and deploys the contract using the Hardhat framework. Once the contract is deployed, its address is logged to the console.
Contract.js Script
The contract.js script contains the necessary functions to interact with the deployed ProfReview smart contract. It includes the following functions:


 1. addReview: This function is used to add a review to the blockchain. It calls the addReview function in the smart contract and passes the required parameters (profID, review, and rating).
2. getReviews: This function retrieves the specific reviews for a professor. It uses the getPastEvents method to filter and collect all the UpdateReviews events for the given professor ID. It then calculates the average rating and returns the list of reviews along with the average rating.
Deployment
The smart contract is deployed on the Goerli test net using the Hardhat framework and Alchemy API. The deployment process is handled by the deploy.js script, which compiles the smart contract and deploys it to the test net.
5. NodeJS Server Development
In this section, we'll discuss the development of the NodeJS server for the "Blockchain-based Rate My Professor" project. The server is built using the Express framework and handles user authentication, interaction with the smart contract, and database queries.
API Endpoints
The server exposes the following main API endpoints:
1. POST /api/addReview: Receives a request to create a new review.
2. GET /api/getAllProfessors: Fetches a list of all professors from the SQLite database.
3. GET /api/getUniversities: Retrieves a list of universities from the SQLite database.
4. GET /api/getReviews/:id: Receives a request to get all reviews for a professor with the
given id.

 Middleware and Third-Party Libraries
The server uses the following middleware and third-party libraries:
● express: The core framework for the server.
● cors: A middleware to handle Cross-Origin Resource Sharing (CORS).
● google-auth-library: A library for verifying Google user authentication tokens.
Error Handling and Logging
Error handling in the server is achieved using try-catch blocks. When an error occurs, it is logged to the console, and an appropriate response is sent back to the client with an error message and an HTTP status code indicating the error.
Performance and Security Considerations
User authentication is handled using Google authentication tokens, ensuring that only users with valid university email addresses can leave reviews. Additionally, the server checks for hate speech in reviews using the ChatGPT API before processing and adding them to the blockchain. These measures help improve the security and quality of the reviews.
User Authentication
User authentication is performed using the Google OAuth2 library. When a user submits a review, the server verifies their Google authentication token to ensure they have a valid university email address. If the token is invalid or the email does not have the appropriate suffix, the server returns an error.
Server Interactions
The server connects to and interacts with the following components:


 1. Smart Contract: The server communicates with the smart contract using the contract.js script. This script exports functions to add reviews to the blockchain and fetch reviews for a given professor.
2. SQLite Database: The server queries the SQLite database using the database.js script, which exports functions to fetch professors and universities. The script also contains functions to add and delete professors from the internal database as well as modify their affiliations with universities. The server uses these functions to handle requests to the /api/getAllProfessors and /api/getUniversities endpoints.
3. ChatGPT API: The server interacts with the ChatGPT API through the ApiServices/index.js script to check if a review contains hate speech.
Overall, the NodeJS server serves as a bridge between the frontend, the smart contract, the SQLite database, and the ChatGPT API, providing a seamless experience for users to rate their professors.
6. Deployment
Deploying the Smart Contract to Mainnet
Deploying the smart contract to the Ethereum Mainnet is a similar process to the deploy.js script used for local testing. However, instead of using a test network, you will need to configure the deployment script to connect to the Ethereum Mainnet. You should also ensure that you have enough Ether in your deployment wallet to cover the gas fees for the deployment.
Setting up and Managing Production Nodes
To set up and manage production nodes, you will need to:
1. Choose a hosting provider, such as AWS, Azure, or GCP.


 2. Configure the server with the necessary resources, such as RAM, storage, and processing power.
3. Install the required software, such as Node.js and any required dependencies.
4. Set up monitoring and alerting tools to track the health and performance of your nodes.
5. Configure automatic backups and updates to keep your nodes secure and up-to-date.
Configuring Network Parameters and Security Best Practices
To ensure the security and performance of your network, you should:
1. Configure a firewall to limit incoming and outgoing traffic to the necessary ports and protocols.
2. Use SSL/TLS certificates to encrypt communication between the server and clients.
3. Use secure authentication methods, such as OAuth2, to protect user accounts and data.
4. Regularly review and update your server and application configurations to stay compliant
with security best practices.
7. Security Best Practices
Private Key Management
Proper private key management is critical for the security of your project. You should:
1. Store private keys securely, using hardware wallets or secure key management systems.
2. Avoid hardcoding private keys in your application or scripts.
3. Regularly rotate private keys and revoke access to compromised keys.
Authentication and Authorization Mechanisms


 Implement strong authentication and authorization mechanisms to protect user data and access to sensitive functionality. You should:
1. Use secure authentication protocols, such as OAuth2 or SAML, for user authentication.
2. Implement role-based access control (RBAC) to limit user access to specific
functionalities.
3. Regularly review and update user access rights to maintain the principle of least
privilege.
Smart Contract and Server Security Considerations
To ensure the security of your smart contract and server, you should:
1. Conduct regular code reviews and security audits.
2. Implement strict input validation and output sanitization to prevent injection attacks.
3. Use secure coding practices and follow established patterns to minimize the potential for
vulnerabilities.
8. Front-End Architecture
The project’s architecture is split up into different files corresponding to the page of the website, with respective css files, and one large file that brings everything together. The primary files are:
1. app.jsx: The main file for the website. This is where we mount our main react component onto our “root” element. This is also where the various router “pathways” are established.
2. Header.jsx: This file handles the header the user sees at the top of the page. It uses ‘Link’ so that the user can move between pages.


 3. Home.jsx: This is the page the user sees when they first enter the website. It communicates with the server to grab a list of all the professors, using ‘Link’ to send the user to the profile page when a professor’s name is clicked. The page also sends the profID with useParams. The last function of the page is that it handles the search component, where the list of professors will change based on the user input.
4. Profile.jsx: Using the profID sent from Home.jsx, this page sends a request to the server for the professor’s reviews and the average grade. It then displays this information along with the professor’s name and current school.
5. NewPost.jsx: This page handles the review submission for the website. Depending on if the user has logged in, the page will either show a message saying to log in, or the page with everything the user needs to add a post. Once the user has inputted the teacher’s name, school, text review, and graded review, a submit button will appear. After the user submits, the page will then route to Home.jsx, eventually routing to Dummy.jsx if hate speech is detected. Unless the page routes to Dummy.jsx, all the information the user inputted will be sent to the server.
6. Login.jsx:
(1) Before starting your work on implementing a Google sign-in system, configure your project on Google Cloud Platform. Choose to configure an OAuth credential project. For our project, we configured the app as “internal” on Google’s end, which allows Google to authorize a successful sign-in to any bc.edu address (the same server as the address provided when configuring this project), while any other account would result in an unsuccessful sign-in. You can choose to configure an “external” project as well, which requires you to register no more than 100 test accounts, which will be the only accounts that will pass the sign-in until you go through a comprehensive process with Google to officially publish your web app. This step will be required when our app expands to involve users from more than one institution.

 (2) After configuring the project, take note of the client_id assigned to your project. In index.html, write
<script src="https://accounts.google.com/gsi/client" async defer></script>
to allow for access to Google's provided scripts.
(3) In Login.jsx, connect to your Google project by using google.accounts.id.initialize(). Then you can render a sign-in button that leads to the sign-in pop-up through google.id.renderButton(). Import jwt_decode, which contains a function for decoding the JSON web token returned from a successful sign-in. By decoding this token, you can obtain the user’s email address, name, profile picture, and some other information.
(4) For our project solution, we store the token as a constant through useState(). The existence of this token will be checked when the user attempts to post a review. Its existence is also a condition we check to decide how we render the Login page. If the user is logged in (thus the token variable exists), we display the user’s name, profile picture, and email address, obtained from decoding the token. We also render a sign-out button that links to a function to terminate the user token. If no token exists, we render the sign-in button and make the New Post display a message asking the user to sign in.
9. Local Website Setup
While at a later stage, we wouldn’t host the server locally, in order to display our project, we did. Follow these steps to get started:
1. Clone the repository: Clone the project repository to your local machine using your preferred Git client or by running the following command in your terminal:
git clone <repository-url>


 2. Install dependencies: Navigate to the project's root directory in your terminal and run the following command to install the required dependencies:
npm install
3. Run the server: Start the server by running the following command in the project's root directory:
PORT=3000 npm start
4. Load the website: In order to run the website, open up your desired browser and visit http://hostsIP:3000/. Keep in mind that the server has to be running in order for the website to work completely.
10. Future Roadmap and Updates
Planned Features and Improvements
The project's theoretical future roadmap includes:
1. Implementing additional filters and search functionality for professors and reviews.
2. Supporting more universities and expanding the database of professors.
3. Incorporating user feedback to improve the platform's user experience.
Contribution Guidelines for External Developers
External developers are encouraged to contribute to the project by:
1. Reporting bugs and security vulnerabilities.
2. Submitting pull requests for new features and improvements.

 
 3. Participating in discussions and providing feedback on the project's direction. Keeping up to Date with Project Updates
Stay informed about project updates by:
1. Following the project's GitHub repository and checking the release notes for new versions.
2. Joining the project's community on social media platforms or forums.
11. Resources and Further Reading
Links to Relevant Documentation, Articles, and Tutorials
1. Ethereum Documentation
2. Solidity Language Documentation
3. Node.js and Express.js Tutorial
4. Google Authentication with Node.js
5. Github Repository
6. Glitch Project
References to External Libraries and Tools Used in the Project 1. Express.js
2. SQLite
3. web3.js
4. Ethers.js

          
 5. Google OAuth2Client
Contact Information for the Development Team
To get in touch with the development team, you can:
1. Reach out via the project's GitHub repository by submitting issues or pull requests.
2. Contact the team via email at nuriyev@bc.edu, anderagh@bc.edu, malx@bc.edu, licjn@bc.edu, and duffybn@bc.edu
By following the guidelines and information provided in these sections, you can better understand the project's architecture, deployment, security, future roadmap, and resources for further learning. This will enable you to contribute to the project, stay up to date with its progress, and utilize it effectively.
Gas Price Analysis
Deploying the Smart Contract
● Gas Usage: 330,749 x 78 = 25,798,422 gwei
● Ethereum Price: 0.025798422 ETH = $47.25
● Polygon Price: 0.025798422 Matic = $0.02463
Average addReview Transaction
● Gas Usage: 29,000 x 78 = 2,262,000 gwei
● Ethereum Price: 0.002262 ETH = $4.11
● Polygon Price: 0.002262Matic = $0.002162
Only takes $1 to produce 462 reviews on average.

 Milestones
Evaluating against our initial specifications
We set out on this project with several specifications in mind. Below is an evaluation of
our project against these requirements.
Requirement 1: The user should be able to leave a review and search reviews for a professor without leaving the website.
The website should prompt the user for login information. There should be a search bar and display of the results where users can search for professors by name. There should be input areas for leaving a review with fields for the professor's name, the course, the rating, the review text, and a button to submit the review. The workflow of the website should be intuitive and students should be able to easily figure out how to navigate the website, leave a review, and search a professor’s review. The website should have clear wayfinding menus to show users where they are.
Project Outcome:
This requirement mainly focuses on the workflow and how users interact with it. The app has a clean interface to facilitate this goal. The wayfinding menu at the top of every page allows the user to easily navigate between pages. As seen in the menu, users can figure out how to navigate the website, leave a review, and search from one place. Through the communication of the front end to the server, the user can see the reviews that are on the blockchain on the website rather than having to be redirected to a site like Etherscan.

 
 Requirement 2: Authentication via BC Login.
We should be able to confirm that a student is a BC-affiliated person using BC Agora
portal login or using Gmail login.
Project Outcome:
We meet this requirement as the website authorizes adding reviews after the user is authenticated through the Google OAuth2 library. If a user is not logged in, they can view reviews but as seen above, they cannot post a review. The Google login, additionally, checks for a bc.edu email. Once you are logged in, the input fields for a review and the button to submit are available.
Requirement 3: Server’s MetaMask identity can call smart contracts.
Our website’s MetaMask account needs to be able to communicate with the smart
contract. Users/students will not need meta masks to leave a review. There will be a MetaMask
account through our server so that the contract can only accept calls from one account.
Project Outcome:
In our design, the only Metamask account was the one connected to the backend, with the private key stored in a dotenv file. In order to simplify the architecture we decided that it was unnecessary for every user/professor to have their own Metamask account. Instead, we simplified that issue by having a single account that is able to add reviews to the contract automatically and also read the reviews from it. This benefits our design in that we only need to supply gas to a single Metamask account and also mitigates the risk of confusion between wallets.

 
 Requirement 4: The smart contract should publish a transaction to the blockchain network.
For the final project, the smart contract needs to be integrated with the MetaMask and
EtherScan. MetaMask needs to send the review submitted by the user to the smart contract,
which will publish it to the chain. This component should allow the MetaMask identity to call
smart contracts to leave reviews.
Project Outcome:
In the beginning, our approach to this goal changed from publishing transactions to emitting an event called UpdateReviews instead. While we did not follow this milestone exactly by using transactions, we chose an alternative route as we learned more about smart contracts and explored multiple approaches. Therefore this project meets the underlying purpose of this goal by having our smart contract deployed to the test net and interacting with it.
Requirement 5: Can search for a professor’s past reviews.
On the website, the user should be able to view all transactions put onto the chain by a
contract by searching the name of a professor.
Project Outcome:
This requirement is met as well. The user is able to search for a professor’s name and select the professor they want. We search by name first rather than searching a school first because when the user goes to leave a review, they already have the professor in mind. If there is only one professor with that name, the user can find the professor in one step. If there are multiple professors, then the user can narrow it down by school. Searching by school will always require two steps. The professor’s page shows all of the reviews on the blockchain left for the selected professor. The professors, universities, courses, and teaching instances are maintained on the database. The server retrieves the list of all professors and universities for the front end where users can search. For each professor, profID is used to retrieve reviews from the blockchain to display on the professor’s website page.
Requirement 6: A professor cannot edit their reviews. Project Outcome:
This goal is met by keeping reviews on the chain; the professor cannot go into the chain to change the content of a review retrospectively. The events emitted to the chain are less at risk of being altered and make the system more secure.
Requirement 7: Transparency of the contract.
Anyone can use Etherscan to audit our smart contract and the reviews left by our users.
Project Outcome:
The contract is transparent and anyone can look it up. The final project does not implement this specification as described in the initial report where we publish the key. At first, the intent was to allow anyone to use Etherscan to audit our smart contract and the reviews left by making the public key public for transparency and accountability.
Requirement 8: Hate speech should not be allowed and should not go on the chain. Project Outcome:
ChatGPT is used to detect the review input from the user for hate speech. We believe this is a significant addition to promoting academic freedom for professors. They should be able to teach without fearing retribution that goes beyond criticism. While developing this project, we received feedback expressing concerns for the professor and the types of things students can write. The goal is to prevent a hostile environment.
Improving the Product
Improvement 1: Only students who have actually taken the professor can leave a review.
Ideally, we would like to be able to verify that a student has taken a course with the
professor before they are able to leave a review on the professor to promote accurate and honest
reviews. We believe that this would require the cooperation of university registrar offices to have
information on course sections, professors, and the students in each section. This may not be
possible to implement within our timeline and with our current resources but is a part of the goal
if we did have enough time and resources.
Improvement 2: Scalable to other universities.
In the long term, the goal is for our solution to be used by other universities and also
across universities. This means verifying different university domains for the Google login for
users. If universities do not use the Google suite, we can use Microsoft or use external sign-in to
connect to school portals. For example, the way you can sign into BC library resources such as
the WSJ. We can use pass-through authentication (PTA) with an external customer validation
source so that students can log in from an external web page.
Improvement 3: Additional search functionality.
The database currently has tables for professors, universities, courses, and teachings (a professor teaching a course at a university). To refine the search for a user, we can filter by a specific course or university. The respective filtering features will be supported on the front end to enable drop-down menus and radio buttons. The search filters would further support scaling to multiple universities to make it easier for users to find what they are looking for.

Gas Prices
Deploying the Smart Contract
● Gas Usage: 330,749 x 78 = 25,798,422 gwei
● Ethereum Price: 0.025798422 ETH = $47.25
27

● Polygon Price: 0.025798422 Matic = $0.02463
Average addReview Transaction
● Gas Usage: 29,000 x 78 = 2,262,000 gwei
● Ethereum Price: 0.002262 ETH = $4.11
● Polygon Price: 0.002262Matic = $0.002162
Only takes $1 to produce 462 reviews on average.
