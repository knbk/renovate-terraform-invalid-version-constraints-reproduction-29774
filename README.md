# Pinning terraform version causes error #29774

Minimal reproduction repository for issue https://github.com/renovatebot/renovate/discussions/29774

## Current behavior

When updating the AWS provider version constraint in the `main.tf` file to `~> 6.0`, the updated version constraints in the lock file `.terraform.lock.hcl` is replaced in-place without normalizing the new constraints according to the rules required by Terraform.

The existing lock file has the following constraints:

```hcl
constraints = "~> 5.0, >= 5.81.0"
```

The updated lock file has the following constraints:

```hcl
constraints = "~> 6.0, >= 5.81.0"
```

This leads to an error when running `terraform init` or `terraform validate`:

```
terraform validate
╷
│ Error: failed to read dependency lock file: Invalid provider version constraints: The recorded version constraints for provider registry.terraform.io/hashicorp/aws must be written in normalized form: ">= 5.81.0, ~> 6.0".
│
│
╵
```

## Expected behavior

The updated lock file should have the constraints normalized to:

```hcl
constraints = ">= 5.81.0, ~> 6.0"
```

The ascending order of the constraints is required by Terraform to avoid the error.

## Link to the Renovate issue or Discussion

https://github.com/renovatebot/renovate/discussions/29774
