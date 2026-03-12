# AWS Service Quotas

- AWS Service Quotas are the maximum number of resources, operations, or items you can create or perform within your AWS account.

**Default:** Every service has a default value set by AWS.

**Applied Quotas:** An applied quota is a specific increase or override granted to your account.

**Regional vs. Global:**
- Most quotas are Region-specific, meaning you must request increases for each AWS Region individually. 
- Some services, like IAM or AWS Organizations, use Global quotas.
- Not all quotas can be increased. You can check if a quota is "Adjustable" in the AWS Console or via the CLI.
- Some service quotes can not be changed.
    - IAM Users: Maximum of 5,000 per account.
    - Managed Policies per Role: Maximum of 10 attached managed policies.
    - Role Trust Policy Length: Maximum of 2,048 characters.
- The higher the increase, the more time is needed to get the change-request approved
- Service endpoint and quotas: [aws-service-information](https://docs.aws.amazon.com/general/latest/gr/aws-service-information.html)
- **Service Quotas**: 
    - From the console we can go to *Service Quotas* page, where we can create dashboards for quotas we want to monitor
    - We can request quota changes from this service for certain services
    - *Quote request template*: we can predefine quota value request for new accounts in an AWS organization
    - We can create a CloudWatch Alarm based on a particular service quota
- Legacy method to increase quotas: create a support ticket selecting service quota increase
- We can request service quota increase from the CLI as well. Use commands like `aws service-quotas list-service-quotas` to view limits or `request-service-quota-increase` to automate requests.

**Automatic Quota Management**
AWS recently introduced Automatic Quota Management, which can proactively: 
- *Notify:* Send alerts (email, SMS, Slack) when usage approaches thresholds like 80% or 95%.
- *Auto-Adjust:* Automatically request increases for supported services based on your actual usage patterns.



<p>
  <a href="../01-accounts/ram.md" style="float: left;">← Previous [RAM]</a>
  <a href="../02-identity/saml.md" style="float: right;">Next [SAML] →</a>
</p>