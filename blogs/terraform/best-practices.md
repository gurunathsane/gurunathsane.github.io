## Terraform best practices

Terraform has gained widespread acceptance as a crucial tool for Infrastructure as Code (IaC) in modern organizations. Whether managing small projects or orchestrating vast infrastructures, Terraform excels in smooth resource management. It leverages the HashiCorp Configuration Language (HCL), a human-readable and straightforward language explicitly designed for IaC. With minimal learning curve, Terraform emerges as a powerful and mature solution.

**Simplicity, Power, and Maturity Unite !**

Terraform's allure lies in its simplicity, yet it remains a robust and mature tool. In this blog, I'll walk you through **essential best practices** to ensure your infrastructure code is not only maintainable and scalable but also secure. If you're already following these practices, great job! If not, I encourage you to incorporate them into your workflows. As always, if I've missed any crucial points, kindly share your insights by sending [mail](mailto:gurunath.sane@gmail.com)

**Happy Terraforming!**

### **1. Organize Your Terraform Code with Modules**

Using modules helps you modularize your Terraform configurations and promotes code reusability. It's essential to break down your infrastructure into smaller, self-contained modules that represent logical components of your system. Modules abstract away the complexity of specific resources and allow you to encapsulate configurations, making them easier to manage, share, and maintain.

You can publish your module and other team members can simply use it. they dont need to re-invent the wheel. With modules you can standardize some components in your organizations. few benefits of using modules are  

- `Code Reusability:` Modules facilitate the reuse of infrastructure configurations across multiple projects and environments. Instead of duplicating the same resource definitions in various configurations, you can create a module once and call it from different places in your codebase.
- `Simplified Configuration:` Modules abstract the details of underlying resources, allowing you to work with high-level abstractions. This simplifies the configuration code, making it easier to understand and manage. 
- `Modular and Scalable:` By organizing your infrastructure code into modular components, you create a more structured and scalable codebase. As your infrastructure grows, you can add or modify modules as needed, without impacting the entire codebase.
- `Promotes Best Practices:` Creating modules encourages the implementation of best practices, as you can enforce certain standards and conventions within the module. This helps in maintaining consistency across the infrastructure.
- `Community and Ecosystem:` Terraform has a vast ecosystem of community-contributed modules available on platforms like the Terraform Registry. You can leverage these pre-built modules to integrate various services and solutions into your infrastructure.
- `Testing and Validation:` You can test and validate modules independently, ensuring they work as expected before integrating them into your main configuration. This promotes a more robust and reliable infrastructure.

Imagine you're tasked with managing AWS resources for a web application. Instead of cramming all resources into a monolithic configuration, consider creating separate modules for distinct components: networking, security groups, and compute. This modular approach enhances organization and reusability.

By breaking down your infrastructure into smaller, self-contained modules, you offer tremendous value to other teams. For instance, your networking module could serve as a blueprint for creating networking components across different projects.

Terraform boasts a thriving community, brimming with over 1500 community-contributed modules. These ready-to-use modules span various cloud providers and services. You can effortlessly leverage them to expedite your deployments or even contribute to the community by enhancing existing modules or publishing your own.

For an extensive list of community-contributed modules, check out the [Terraform Registry](https://registry.terraform.io/browse/modules). There, you'll discover an array of pre-built modules to augment your infrastructure projects.

This [official documentation](https://developer.hashicorp.com/terraform/language/modules) put more light on terraform modules

### **2. Leverage Input Variables and Outputs**

Use input variables to parameterize your code or modules, making them more flexible and allowing for easy configuration changes across environments. Outputs enable you to share data between modules and access information from one configuration in another. With proper parameterization you can use same code base for multiple environments.  

In your EC2 instance module, define input variables like instance type, AMI ID, and security group IDs. Then, use outputs to expose the instance's public IP or its DNS name for other modules to consume.

You can get more details on Terraform variables on this [official documentation page](https://developer.hashicorp.com/terraform/language/values)

### **3. Version Control Your Infrastructure Code**

Just like application code, infrastructure code should be version-controlled to track changes and collaborate effectively. Use a version control system (e.g., Git) to manage your Terraform configurations. 

To maintain a structured and collaborative Terraform development process, follow Git best practices similar to managing application codebases. Keep your Terraform code in a Git repository and commit changes frequently. Leverage branches for new features and bug fixes, and adopt a pull request workflow for code reviews. Emphasize clear and descriptive commit messages for better tracking and collaboration.

By adhering to these simple but effective Git practices, you can ensure a seamless and organized Terraform development experience, just like you would with any other application code.

### **4. Use Terraform Workspaces for Environments**

Terraform workspaces allow you to manage multiple environments (e.g., dev, staging, prod) with the same configuration. Utilize workspaces to switch between environments easily and avoid duplicating configurations.

Create workspaces named `dev`, `staging`, and `prod`. Set different variables, such as region or instance count, for each workspace to tailor the infrastructure to its specific environment.

This [official documentation](https://developer.hashicorp.com/terraform/language/state/workspaces) provides insights into Terraform workspaces, showcasing their powerful capabilities

### **5. Store Terraform State Securely and Wisely**

terraform state file is a key thing which you should manage very carefully. By default, Terraform keeps the state file locally on the machine where Terraform is executed. However, as projects and teams scale, sharing and managing the state file across multiple team members and environments becomes challenging. 

More ever, Terraform state files contain sensitive information, such as resource IDs and credentials. 

Choose an appropriate backend configuration to store your terraform state files based on your use case. Local backends work well for personal projects, but for collaboration, opt for remote backends like Terraform Cloud or AWS S3, HashiCorp Consul) to prevent accidental exposure.

Terraform provides wide range of remote backend storage including cloud provider storage and databases, or even git repository. The number of backend options are increasing day to day. 

This [official page](https://developer.hashicorp.com/terraform/language/settings/backends/configuration) provides detailed information about Terraform backends.

[My Blog on Terraform backends](./backend.html) talks more about terraform backends
### **6. Lock Versions**

To prevent unexpected behavior due to version mismatches, lock the Terraform version used in your project. It is also recommended to lock your provider and even modules version. 

In terraform block (generally in `main.tf` file at root of your project) specify `required_version` as described bellow: 
```
terraform {
  required_providers {
    aws = {
      version = "~> 2.13.0" //this line specify aws provider version 
    }
  }

  required_version = "~> 0.12.29"
}
```
This [official tutorial](https://developer.hashicorp.com/terraform/tutorials/configuration-language/versions) describes more about terraform versions

### **7. Implement Continuous Integration (CI) for Terraform**

Incorporate Terraform into your CI pipeline to automatically test, validate, and apply changes. This helps catch errors early and ensures a consistent deployment process. 

Additionally, you can allow any changes in Production environment from CI job only. All humans should have Read only access for production environment. All changes in production assets should apply from CI job only. This helps to minimize human errors, speedy delivery and track all changes done in environment. It also simplifies rollback any changes if something goes wrong. 

your CI job should run `terraform validate` and `terraform plan` before `terraform apply` command. Add manual approvals before running `terraform apply` command.  

### **8. Plan and Apply Changes with Care**

Always review the Terraform plan before applying changes to your infrastructure. Understand what resources will be created, modified, or deleted to avoid unintended consequences. For production environments, dont allow any humans to update resources. All changes to production systems should come from terraform run from CI-CD jobs with manual approvals steps. 

### **9. Regularly Update Terraform Providers and Modules**

Keep your Terraform, Terraform providers and modules up-to-date to access the latest features, bug fixes, and security updates.

`terraform init -upgrade` command helps you to update your provider versions. Monitor for new releases of modules you use and upgrade them when needed.

### **10. Do not Hardcode Sensitive Information**
Never hardcode sensitive data like passwords or access keys directly in your Terraform configuration. Instead, use environment variables or external configuration management tools. Avoid Hardcoding any data even if it is not sensitive in terraform scripts. Try to use data blocks, input and output variables to make your project more flexible.

### **11. Do not Ignoring Terraform Warnings and Errors**

Never ignore Terraform warnings or errors in the plan output. These messages indicate potential issues that should be addressed before applying changes. Sometimes warning tell deprecated properties or best practices to configure resources properties

### **12. Define Resource Dependencies**
Ensure that resource dependencies are explicitly defined in your configuration. Ignoring dependencies can cause resources to be created in the wrong order or create other unforeseen issues. You can use depends_on property to define your own order.

### **13. Do not edit State file manually**
Editing the Terraform state file manually is strongly discouraged and can lead to serious issues with your infrastructure management. The state file is a critical component of Terraform's operation, and any manual changes can cause unexpected and potentially destructive consequences. Here are some of the problems that may occur if you edit the Terraform state file manually: 
- `Inconsistencies` in state file and actual resource state. his can result in Terraform being unable to accurately manage and update the infrastructure.
- `State Corruption` The state file is in a specific format and structure that Terraform relies on to understand the current state of resources. Making incorrect changes to the state file can corrupt the data, rendering it unusable by Terraform.
- `Data Loss` Incorrect manual edits to the state file can cause Terraform to lose track of resources, leading to data loss, accidental resource deletion, or overwriting existing resources.

Always use Terraform commands to manage the state file.

### **14. Use external tools**
To make sure you are writing terraform code which spin ups resources in correct way. You can use open source tools like [checkov](https://www.checkov.io/). Checkov is static code analysis tool for terraform and cloudformation. It also suggest good practices and security glitches in your terraform code. Nice part is checkov also comes as `vscode plugin`. This makes easy to scan code right from your IDE while writing code. 

[snyk](https://snyk.io/), [Terraform Linter](https://github.com/terraform-linters/tflint) are similar tools to scan your terraform code. infra-cost is another tool for static analysis the code.


## Conclusion 
By following these Terraform best practices, you can create robust and manageable infrastructure as code. Terraform's flexibility and power, combined with these best practices, will help you build and maintain a reliable and scalable infrastructure for your applications. 



**Once again Happy Terraforming!**

-