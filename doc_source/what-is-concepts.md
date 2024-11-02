# Serverless concepts<a name="what-is-concepts"></a>

## Serverless concepts<a name="what-is-concepts-terms"></a>

**Event\-driven architecture**  <a name="what-is-concepts-terms-eda"></a>
* AWS services / -- based on -- serverless application
  * _Example:_
    * AWS Lambda
      * for compute
    * Amazon DynamoDB
      * for database management
  * -- are loosely integrated through an event-driven architecture with -- EACH other
  * see [What is an Event\-Driven Architecture?](https://aws.amazon.com/event-driven-architecture/)\. 

**Infrastructure as Code \(IaC\)**  <a name="what-is-concepts-terms-iac"></a>
Infrastructure as Code \(IaC\) is a way of treating infrastructure in the same way that developers treat code, applying the same rigor of application code development to infrastructure provisioning\. You define your infrastructure in a template file, deploy it to AWS, and AWS creates the resources for you\. With IAC, you define in code what you want AWS to provision\. For more information, see [Infrastructure as Code](https://docs.aws.amazon.com/whitepapers/latest/introduction-devops-aws/infrastructure-as-code.html) in the *Introduction to DevOps on AWS* AWS Whitepaper\.

**Serverless technologies**  <a name="what-is-concepts-terms-serverless"></a>
* allows
  * build and run applications / WITHOUT having to manage your own servers
    * == server management -- is done by -- AWS
  * automatic scaling
  * built\-in high availability
* see  
  + [Serverless on AWS](https://aws.amazon.com/serverless/)
  + [Serverless Developer Guide](https://docs.aws.amazon.com/serverless/latest/devguide/serverless-preface.html)
    + == conceptual overview of serverless development | AWS Cloud
  + [Serverless 101: Understanding the serverless services](https://serverlessland.com/learn/serverless-101)
