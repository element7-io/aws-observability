# Service Discovery for Amazon ECS (made easy)

This slightly enhanced version of the CloudWatch agent brings Service Discovery for ECS.

From : [Detailed guide for autodiscovery on Amazon ECS
clusters](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContainerInsights-Prometheus-Setup-autodiscovery-ecs.html)
> **There is no built-in service discovery for Amazon ECS. The CloudWatch agent adds this mechanism.**


## Install the CloudWatch agent
The [AWS CloudWatch agent installation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContainerInsights-Prometheus-install-ECS.html) procedure as described by AWS is somewhat cumbersome IMHO. This repository contains a refactored solution that should make installation a tad easier.

## Configuration

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

Introduce the possibility to block the enrolment of the artifacts bucket on the account level.


