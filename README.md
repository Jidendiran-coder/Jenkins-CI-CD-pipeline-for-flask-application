# Jenkins CI CD pipeline for Flask application

## Project Setup

### Prerequisites
- Docker
- Docker Compose
- Git

### Installation Steps

1. Clone the repository
```bash
git clone <your-repo-url>
cd Jenkins-CI-CD-pipeline-for-flask-application
```

2. Environment Configuration
- No manual `.env` file is required
- Environment variables are managed through Docker Compose
- Key configurations are set in `docker-compose.yml`

3. Build and Start the Application
```bash
# Build and start all services
docker-compose up --build

# Or run in detached mode
docker-compose up -d --build
```

4. Accessing the Application
- Application runs on `http://localhost:8000`
- Swagger UI available at `http://localhost:8000/swagger`

### Running Tests

```bash
# Run all tests
docker-compose run --rm test

# Run specific test files
docker-compose run --rm test pytest tests/test_auth.py
```

### API Endpoints

- `POST /register`: User registration
- `POST /login`: User login (returns JWT token)
- `GET /profile`: Get user profile (requires JWT)
- `PUT /profile`: Edit user profile (requires JWT)
- `POST /change-password`: Change user password (requires JWT)
- `POST /logout`: Logout user (client-side token removal)

### Authentication
- Use JWT token in Authorization header for protected routes
- Token format: `Authorization: Bearer <your_token>`

### Local Development Workflow
1. Make code changes
2. Rebuild containers: `docker-compose up --build`
3. Run tests: `docker-compose run --rm test`
4. Access application at `http://localhost:8000`

### Docker Services
- `web`: Main Flask application
- `mongo-db`: MongoDB database
- `test`: Container for running tests

### Stopping the Application
```bash
# Stop and remove containers
docker-compose down

# Stop and remove containers with volumes
docker-compose down -v
```

### Running Tests
To run all tests:
```bash
pytest
```

To run specific test files:
```bash
# Run authentication tests
pytest tests/test_auth.py

# Run profile tests
pytest tests/test_profile.py
```

### Test Coverage
- Authentication tests cover:
  - User registration
  - Login
  - Invalid login scenarios
- Profile tests cover:
  - Retrieving profile
  - Updating profile
  - Changing password
  - Unauthorized access checks

### Notes
- Tests use a separate test database
- Test data is automatically cleaned up after each test run

## Jenkins CI/CD Pipeline Overview

This project implements a comprehensive Continuous Integration and Continuous Deployment (CI/CD) pipeline using Jenkins, demonstrating a robust automated workflow for a Flask application.

### Pipeline Stages

#### 1. Get Build Number
- Captures and logs the unique build number for tracking and identification

#### Output
This screenshot illustrates the first stage of the Jenkins pipeline, which captures and logs the unique build number. The build number serves as a critical identifier for tracking each specific build and deployment iteration.

<img width="1290" height="339" alt="1_get_build_no" src="https://github.com/user-attachments/assets/e079dba6-867c-4456-a344-e972109a648a" />

#### 2. Checkout Code
- Pulls the latest code from the main branch of the GitHub repository

#### Output
This screenshot demonstrates the Checkout Code stage, where Jenkins pulls the latest code from the main branch of the GitHub repository. This ensures that the most recent version of the codebase is used for building, testing, and deployment.

<img width="1452" height="652" alt="2_checkout_code" src="https://github.com/user-attachments/assets/29a5e17e-0899-440b-ad46-06945805002d" />

#### 3. Run Tests
- Sets up a MongoDB container with seed data
- Runs pytest with the following characteristics:
  - Generates XML test reports
  - Stops on first test failure
  - Captures and reports test results
- Ensures clean environment by tearing down containers after testing

#### Output - Successful Test
This screenshot shows a successful test run, demonstrating that all tests passed. The green indicators and test report confirm the application's functionality and code quality.

<img width="1386" height="584" alt="3_run_test" src="https://github.com/user-attachments/assets/c67d8c61-be47-418e-9a3b-bcc4240d5a20" />

#### Output - Successful Test Report

<img width="1457" height="418" alt="test_results" src="https://github.com/user-attachments/assets/cc6b5bc8-405c-4c18-af04-1ae6a8e64687" />

#### 4. Build Docker Image
- Builds a Docker image for the Flask application
- Tags the image with the current build number

#### Output
This screenshot captures the Docker image build stage, where a Docker image is created for the Flask application. The build process compiles the application, installs dependencies, and packages the entire application into a portable, reproducible container.

<img width="1277" height="545" alt="4_build_docker" src="https://github.com/user-attachments/assets/364a6460-0d58-486a-b017-712c47418004" />

#### 5. Test Docker Credentials
- Validates Docker Hub credentials
- Ensures secure authentication for image push and pull operations

#### Output
This screenshot demonstrates the Docker credentials testing stage, which validates the Docker Hub authentication credentials. This critical security step ensures secure and authorized access to Docker Hub for pushing and pulling container images.

<img width="1299" height="578" alt="5_test_docker" src="https://github.com/user-attachments/assets/c9cf8b32-3d14-48e4-8968-418f0216e0ab" />

#### 6. Push Docker Image
- Pushes the built Docker image to Docker Hub
- Uses secure credential management

#### Output - Stage
This screenshot illustrates the Push Docker Image stage, where the newly built Docker image is securely uploaded to Docker Hub. This step ensures that the latest version of the application is available for deployment and can be easily pulled by other systems or team members.

<img width="1325" height="633" alt="6_push_docker" src="https://github.com/user-attachments/assets/6bb4d8f3-ab32-4582-a8b8-4bd68be95a48" />

#### 7. Deploy to EC2
- Connects to a predefined EC2 instance via SSH
- Creates/checks Docker network and volumes
- Ensures MongoDB container is running
- Pulls the latest Docker image
- Stops and removes any existing container
- Starts a new container with:
  - Proper network configuration
  - Environment variables
  - Port mapping

#### Output
This screenshot depicts the Deploy to EC2 stage, which demonstrates the automated deployment process to the target EC2 instance. The image shows the successful SSH connection, Docker image pull, and container startup, highlighting the seamless and reproducible deployment workflow.

<img width="1282" height="645" alt="7_deploy_ec2" src="https://github.com/user-attachments/assets/39aef96c-cfcf-42ff-851c-426250866b52" />

### Notification System
- Sends email notifications for:
  - Build failures
  - Successful deployments

### Post-Deployment Actions
- Generates a detailed deployment success summary
- Includes information about:
  - Docker images
  - Running containers
  - Disk space
  - Docker system info
- Performs cleanup by removing older Docker images

#### Output - Clean the Docker images

This screenshot shows the Docker image cleanup stage, which is an essential part of maintaining a clean and efficient Docker environment. After successful deployment, the pipeline removes older Docker images to prevent disk space accumulation and keep the system optimized.

<img width="1445" height="637" alt="8_post_clean_image" src="https://github.com/user-attachments/assets/eea414b6-9944-4b6d-8d27-8ed12a96ff3a" />

### Environment Variables
Key environment variables used in the pipeline:
- `GITREPO`: Source code repository
- `EC2_HOST`: Deployment target EC2 instance
- `DOCKER_IMAGE`: Docker image name
- `CONTAINER_NAME`: Name of the deployed container
- `ALERT_EMAIL`: Notification email address

### Prerequisites
- Jenkins
- Docker
- Docker Compose
- EC2 Instance
- Docker Hub Account

### Deployment Workflow
1. Code is pushed to the main branch
2. Jenkins automatically triggers the pipeline
3. Tests are run
4. Docker image is built and pushed
5. Application is deployed to EC2
6. Notifications are sent

## Local Development
Refer to the `docker-compose.yml` for local setup instructions.

## Contributing
Please read the contributing guidelines before submitting pull requests.
