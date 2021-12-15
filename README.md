## 1, Enable Service Discovery for Amazon ECS

A key component of this observability setup is Service Discovery setup for ECS. In order to 
allow Prometheus to do its job it needs a to know the private ip's of the various containers running
on ECS.  Sadly Service Dsicovery for ECS doesn't come out of the box (yet).

From : [Detailed guide for autodiscovery on Amazon ECS
clusters](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContainerInsights-Prometheus-Setup-autodiscovery-ecs.html)
> **There is no built-in service discovery for Amazon ECS. The CloudWatch agent adds this mechanism.**

AWS itself has created a CloudFormation stack in order to make Service Discovery on ECS possible:
[AWS CloudWatch agent installation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContainerInsights-Prometheus-install-ECS.html) 

The procedure AWS describes is somewhat cumbersome IMHO. Therefor the first component in this setup 
is a refactored solution of that statck that should make installation a tad easier.




### Configuration

```
{
    "logs": {
        "force_flush_interval": 5,
        "metrics_collected": {
            "prometheus": {
                "ecs_service_discovery": {
                    "docker_label": {},
                    "sd_frequency": "1m",
                    "sd_result_file": "/tmp/cwagent_ecs_auto_sd.yaml",
                    "task_definition_list": [
                        {
                            "sd_job_name": "discover-prometheus",
                            "sd_metrics_path": "/metrics",
                            "sd_metrics_ports": "9090",
                            "sd_task_definition_arn_pattern": ".*"
                        },
                        {
                            "sd_job_name": "discover-yace",
                            "sd_metrics_path": "/metrics",
                            "sd_metrics_ports": "5000",
                            "sd_task_definition_arn_pattern": ".*:task-definition/yace:[0-9]+"
                        },
                        {
                            "sd_job_name": "discover-cost-exporter",
                            "sd_metrics_path": "/metrics",
                            "sd_metrics_ports": "5000",
                            "sd_task_definition_arn_pattern": ".*:task-definition/cost-exporter:[0-9]+"
                        }
                    ]
                },
                "prometheus_config_path": "/opt/aws/amazon-cloudwatch-agent/bin/prometheus.yml"
            }
        }
    }
}
```

## Install the CloudWatch agent

