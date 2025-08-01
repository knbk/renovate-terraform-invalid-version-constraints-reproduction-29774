# Updated constraints in Terraform lock file have to be normalized #37263

Minimal reproduction repository for issue https://github.com/renovatebot/renovate/discussions/37263

## Current behavior

Updating the required version from `~> 5.0` to `~> 6.0` will apply the same change in the lock file, resulting in the following constraints:

```hcl
provider "registry.terraform.io/hashicorp/aws" {
  version     = "6.6.0"
  constraints = "~> 6.0, >= 5.81.0"
  hashes = [...]
}
```

Terraform is very strict about its lock file format. The constraints must be written in a "normalized" form, or Terraform will throw an error and refuse to run:

```
│ Error: failed to read dependency lock file: Invalid provider version constraints: The recorded version constraints for provider registry.terraform.io/hashicorp/aws must be written in normalized form: ">= 5.81.0, ~> 6.0".
```

## Expected behavior

Renovate normalizes the constraints according to Terraform's rules:

```hcl
provider "registry.terraform.io/hashicorp/aws" {
  version     = "6.6.0"
  constraints = ">= 5.81.0, ~> 6.0"
  hashes = [...]
}
```

This normalized form does not appear to be publicly documented. However, the function that performs the normalization is located here: https://github.com/hashicorp/terraform/blob/eaf3b90bc3def205dd7b060e8608fb203fd127d9/internal/getproviders/providerreqs/version.go#L124.

## Link to the Renovate issue or Discussion

https://github.com/renovatebot/renovate/discussions/37263 (main issue)
https://github.com/renovatebot/renovate/discussions/29774 (previous discussion)
