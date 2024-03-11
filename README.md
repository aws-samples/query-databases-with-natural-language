# Transforming the Way We Talk to Databases: Using Everyday Language to Search and Retrieve Data with Mixtral 8x7B

This project enables interacting with relational databases through Natural Language. Specifically, it uses the sparse Mixture of Experts (MoE) model Mixtral 8x7B, for generating SQL queries, and interpreting their corresponding tabular results to return to the user as answers. The application leverages SageMaker hosting tools to serve the model, which is deployed with a few clicks from SageMaker JumpStart. For more details, please refer to [this blog post().

## Setup Requirements

You can currently deploy Mixtral 8x7B on SageMaker Jumpstart with one click. Amazon SageMaker JumpStart provides a simplified way to access and deploy over 100 different open source and third-party foundation models. In order to [launch an endpoint to host Mixtral 8x7B from SageMaker JumpStart](https://docs.aws.amazon.com/sagemaker/latest/dg/jumpstart-deploy.html), you may need to request a service quota increase to access an ml.g5.48xlarge instance for endpoint usage. You can easily [request service quota increases](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-quota-increase.html) through the AWS console, CLI, or API to allow access to those additional resources.

You will need access to a relational data source that can connect with the SQLDatabase Langchain module. Amazon Redshift is used as the primary data source in this post with the [TICKIT database](https://docs.aws.amazon.com/redshift/latest/dg/c_sampledb.html). This database helps analysts track sales activity for the fictional TICKIT web site, where users buy and sell tickets online for sporting events, shows, and concerts. In particular, analysts can identify ticket movement over time, success rates for sellers, and the best-selling events, venues, and seasons. Analysts can use this information to provide incentives to buyers and sellers who frequent the site, to attract new users, and to drive advertising and promotions.

_Please review any license terms applicable to the dataset with your legal team and confirm that your use case complies with the terms before proceeding._

You can also experiment with other AWS data sources like Amazon RDS and Athena or even your own relational databases. The LangChain module supports any database with a JDBC or ODBC driver and SQL access. Make sure to have the connection details handy for your chosen data source, such as database URL, username, and password.

## Architecture

![](docs/architecture1.png)

At a high level, Text2SQL solutions such as the one in this repository, consist of three core components:

1. **Structured Data Source**: This can be any relational data source such as Amazon RDS, Amazon Aurora, AWS Athena, or Snowflake. It contains the business data to query.

2. **Foundation Model**: A large language model (LLM) that is able to understand the data schema of the source database and map natural language questions into corresponding SQL queries.

3. **Orchestrator Back-end**: An orchestration layer built using AWS services like Lambda or SageMaker Notebooks. It receives the user question, passes it to the LLM to generate SQL, executes the SQL against the database, then passes the results back to the language model to interpret and respond in plain English.

In the code provided in this repository, the architecture is the following:

![](docs/architecture2.png)

With these configuration steps:

- **Setting up a Redshift Cluster**: In this code sample we have setup a RA3 type cluster. We’ll load the TICKIT sales dataset into the Redshift cluster from here.

- Setting up a SageMaker Domain and cloning this GitHub repository into SageMaker Studio Classic.

- Deploying the Mixtral 8x7B Instruct model from SageMaker JumpStart.
