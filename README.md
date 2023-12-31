# ETLJob_StreamingJob_Workflow

![image](https://github.com/phaniteja5789/ETLJob_StreamingJob_Workflow/assets/36558484/2c3e1909-0103-45d5-a6fe-fa25f485f3df)

The StepFunction code is attached in both the formats JSON and YAML and present in the files **StateMachine.Json as well as StateMachine.YAML** under the same repository

This entire workflow has been developed using **AWS Step Functions** with appropriate permissions and roles

Workflow has been divided into 2 parts <br/>
1.) ETL WorkFlow <br/>
2.) Streaming WorkFlow <br/>

Workflow will be identified based on intrinsic step functions (States.MathRandom(StartValue, EndValue))

**Used AWS Services**

**Step Functions, Lambda, KINESIS, SNS, S3, Glue, IAM**

**Used Intrinsic Functions in the Step Function**

**States.Format, States.MathRandom, States.StringToJson, States.JsonToString etc** 

Used **InputPath, Parameters** for Input Filteration and Transformation of Input from one form to another form <br/>

Used **ResultPath, and OutputSelector** to filter capture both input and output for the next state. <br/>

**Input Data given to the State Machine**. <br/>
The Sample data has been given in the **InputDataToStepFunction.txt** in the same repository <br/>

Role details has been present in the GlueRoleToAccessS3.txt and StepFunctionRoleToAccessAllUsedServices.txt <br/>
1.) In GlueRoleToAccessS3.txt, specifying the AWS Glue Job to use S3 service <br/>
2.) In StepFunctionRoleToAccessAllUsedServices.txt, specifying the StepFunction to use required service with necessary permission. <br/>

**Streaming WorkFlow** <br/>
1.) The Streaming Workflow execution starts with Pass State and passes to the next state to Invoke Lambda Function with Function Name (**FetchSubscriptionDataFunction**) <br/>
2.) In the FetchSubscriptionDataFunction, a layer is created for the usage of the **Requests** Module and attached to the Lambda Function. In this function with the help of **RapidAPI**, the data is fetched from an open endpoint regarding the OTT Platform Subscription details for each and every country. But in the code, it will be handled only for a country. <br/>
3.) Data will be fetched and returns the output from the LambdaInvoke State will be sent to next state <br/>
4.) In **Kinesis ListStreams** State, with the help of AWS SDK fetching the list of streams present under the account <br/>
5.) If the stream that is passed from input is already present in the stream, then write the result of lambda function directly to the Stream <br/>
6.) If the stream is not present then create the stream with the input stream name, and write the result of lambda function into the stream <br/>
7.) Once the **DataRecord** is inserted into the **DataStream** we will be moving to next state <br/>
8.) In next state, we are using **ListTopics** from **SNS Service** to get list of topics under the account <br/>
9.) If the Topic name recieved from the Input is already present then we assume that the **Subscriber** is already present <br/>
10.) If the Topic is not present, then we are creating a topic based on the input, and creating a **Subscriber with Email Protocol** based on the Input data where we are sending the Subscriber Email address <br/>
11.) Once the topic is created subscription is confirmed then we **Publish the data into the Topic** <br/>
12.) In the Data published, we will be sending the **ShardId with the Sequence number of the Kinesis Data Stream**, where our data is stored. <br/>
13.) After that we mark the Streaming workflow as Success with **Success State**. <br/>

**ETL Job WorkFlow** <br/>
1.) The ETL Workflow execution starts with Pass State and passes to the next state to Invoke Lambda Function with Function Name (**FetchStockDataFunction**) <br/>
2.) In the **FetchStockDataFunction**, a layer is created for the usage of the **Requests** Module and attached to the Lambda Function. In this function with the help of **RapidAPI**, the data is fetched from an open endpoint regarding the stock information details for each and every minute. <br/>
3.) The Lambda Function returns the result and the data will be passed to the next state <br/>
4.) In **S3 ListBuckets** State, it will lists all the buckets present under the account <br/>
5.) If the bucket that is passed as input is already present in the account, then it directly **put objects** in the Bucket <br/>
6.) If the bucket is not present, then it will create the Bucket and **put objects** in the Bucket <br/>
7.) In the next state, it will start the **Glue Job Run** with the name **(StockDataETLJob.py)** which is already created under the account in **PySpark** <br/>
8.) Once the Job executes it will create the results under **S3 Bucket**. <br/>

The Execution results for both the workflows are attached below <br/>

**Streaming WorkFlow Result** <br/>

![image](https://github.com/phaniteja5789/ETLJob_StreamingJob_Workflow/assets/36558484/411769e6-d2b6-4b8a-bb4e-5d52644f8c99)

**ETL WorkFlow Result** <br/>

![image](https://github.com/phaniteja5789/ETLJob_StreamingJob_Workflow/assets/36558484/f78d69bf-2d35-4f60-867c-58174b49e11a)


**Programming Languages & Technologies used in this project are** <br/>

**Python
PySpark
AWS**
