# Short-url-backend — A backend service for generating and managing short URLs
## Overview
The Short-url-backend project provides a simple and efficient way to create and manage short URLs. It utilizes the Express framework to handle HTTP requests and responses. This service aims to provide a scalable and reliable solution for URL shortening.

## Tech Stack
* Node.js
* npm
* Express

## Prerequisites
Node.js and npm must be installed on the system.

## Getting Started
To get started, clone the repository using `git clone https://github.com/your-username/Short-url-backend.git`. Then, install the dependencies using `npm install`. Configure the environment variables as needed. Finally, run the application using `npm start`.

## Environment Variables
| Variable | Default | Description |
| --- | --- | --- |
| PORT | 3000 | The port number to listen on |

## API Reference
The API provides the following endpoints:
* `POST /shorten`: Create a new short URL
* `GET /:shortUrl`: Redirect to the original URL

## Testing
To run tests, use the command `npm test`.

## Contributing
Contributions are welcome and appreciated. To contribute, please fork the repository, make the necessary changes, and submit a pull request. Ensure that all tests pass and the code is properly formatted before submitting.