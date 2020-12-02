# Terraform

## Tricks

### Workspaces List Shell Scripting

[bash-script-terraform-workspace-list](https://discuss.hashicorp.com/t/bash-script-terraform-workspace-list/6498)

```bash
# Use like this. With quotes
echo "$(terraform workspace list)"
```

### Dynamic optional blocks

[Optional dynamic blocks - depending on existence of map keys](https://discuss.hashicorp.com/t/pattern-to-handle-optional-dynamic-blocks/2384)

[Conditional nested blocks in Terraform](https://codeinthehole.com/tips/conditional-nested-blocks-in-terraform/)

* Example

```hcl-terraform
dynamic load_balancer {
    for_each = local.services[count.index].alb_create ? [1] : []
    content {
      target_group_arn = local.services[count.index].alb_create ? aws_lb_target_group.this[count.index].arn : ""
      container_name   = local.services[count.index].name
      container_port   = local.services[count.index].container_port
    }
  }
```