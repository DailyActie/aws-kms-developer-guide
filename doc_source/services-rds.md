# How Amazon Relational Database Service \(Amazon RDS\) Uses AWS KMS<a name="services-rds"></a>

You can use the [Amazon Relational Database Service \(Amazon RDS\)](https://aws.amazon.com/rds/) to set up, operate, and scale a relational database in the cloud\. Optionally, you can choose to encrypt the data stored on your Amazon RDS [DB instance](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.DBInstance.html) under a customer master key \(CMK\) in AWS KMS\. To learn how to encrypt your Amazon RDS resources under a KMS CMK, see [Encrypting Amazon RDS Resources](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html) in the *Amazon Relational Database Service User Guide*\.

Amazon RDS builds on [Amazon Elastic Block Store \(Amazon EBS\) encryption](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html) to provide full disk encryption for database volumes\. For more information about how Amazon EBS uses AWS KMS to encrypt volumes, see [How Amazon Elastic Block Store \(Amazon EBS\) Uses AWS KMS](services-ebs.md)\.

When you create an encrypted DB instance with Amazon RDS, Amazon RDS creates an encrypted EBS volume on your behalf to store the database\. Data stored at rest on the volume, database snapshots, automated backups, and read replicas are all encrypted under the KMS CMK that you specified when you created the DB instance\. 

## Amazon RDS Encryption Context<a name="rds-encryptioncontext"></a>

When Amazon RDS uses your KMS CMK, or when Amazon EBS uses it on behalf of Amazon RDS, the service specifies an encryption context\. The encryption context is additional authenticated information that AWS KMS uses to ensure data integrity\. That is, when an encryption context is specified for an encryption operation, the service must specify the same encryption context for the decryption operation or decryption will not succeed\. The encryption context is also written to your [AWS CloudTrail](https://aws.amazon.com/cloudtrail/) logs to help you understand why a given CMK was used\. Your CloudTrail logs might contain many entries describing the use of a CMK, but the encryption context in each log entry can help you determine the reason for that particular use\.

At minimum, Amazon RDS always uses the DB instance ID for the encryption context, as in the following JSON\-formatted example:

```
{ "aws:rds:db-id": "db-CQYSMDPBRZ7BPMH7Y3RTDG5QY" }
```

This encryption context can help you identify the DB instance for which your CMK was used\.

When your CMK is used for a specific DB instance and a specific EBS volume, both the DB instance ID and the EBS volume ID are used for the encryption context, as in the following JSON\-formatted example:

```
{
  "aws:rds:db-id": "db-BRG7VYS3SVIFQW7234EJQOM5RQ",
  "aws:ebs:id": "vol-ad8c6542"
}
```