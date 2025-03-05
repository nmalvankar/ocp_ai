# Model Bias Monitoring in OpenShift AI

Red Hat OpenShift AI provides tools and features that allow you to monitor your models for bias. This document explains how to create, view, and monitor bias metrics for your deployed models in OpenShift AI.

## Key Concepts
- **Bias Metrics:** Metrics that help evaluate the fairness of a model, such as **Statistical Parity Difference** and **Disparate Impact Ratio**.
- **Protected Attributes:** The characteristics (e.g., gender, race) that may be the subject of bias analysis.
- **Privileged and Unprivileged Groups:** Groups within a protected attribute (e.g., male vs. female).
- **Outcome:** The prediction or decision made by the model.

## How to Create a Bias Metric in OpenShift AI

To monitor a deployed model for bias in OpenShift AI, you must first create a bias metric. When creating a bias metric, you specify details relevant to your model, such as a protected attribute, privileged and unprivileged groups, a model outcome, and the acceptable threshold for bias.

### Supported Bias Metrics:
- **Statistical Parity Difference**
- **Disparate Impact Ratio**

### Steps to Create a Bias Metric:
1. **Install Required Libraries**  
   If you are using a Jupyter Notebook or custom model deployment, you need to install the AI Fairness 360 (AIF360) and other necessary libraries:

   ```bash
   pip install aif360 scikit-learn pandas numpy
Load Your Model and Data
Make sure your model is deployed in OpenShift AI, either via ModelMesh Serving or KServe. Here’s an example using AIF360 in Python:

python
Copy
Edit
from aif360.datasets import AdultDataset
from aif360.metrics import BinaryLabelDatasetMetric

# Load your dataset
dataset = AdultDataset()

# Define the privileged and unprivileged groups
privileged_groups = [{'sex': 1}]  # Example: Male
unprivileged_groups = [{'sex': 0}]  # Example: Female

# Compute bias metrics
metric = BinaryLabelDatasetMetric(dataset, 
                                  privileged_groups=privileged_groups, 
                                  unprivileged_groups=unprivileged_groups)

print("Disparate impact:", metric.disparate_impact())
print("Statistical parity difference:", metric.statistical_parity_difference())
Integrate with OpenShift AI

Attach a fairness validation pipeline in Red Hat OpenShift Pipelines.
Monitor and log bias metrics using Prometheus and Grafana.
Modify inference requests to check for fairness in real-time.
Automate Bias Detection in OpenShift Pipelines
Add a bias-checking step in OpenShift Pipelines (Tekton) to analyze bias before deployment:

yaml
Copy
Edit
steps:
  - name: bias-metrics
    image: python:3.9
    script: |
      pip install aif360 scikit-learn pandas numpy
      python bias_analysis.py  # Run the bias detection script
Visualize Bias Metrics
To track fairness over time:

Use Grafana dashboards with Prometheus metrics.
Export metrics to OpenShift AI Model Registry.
How to View Bias Metrics in OpenShift AI
Once your model is deployed and bias metrics are created, you can view and monitor the bias metrics in OpenShift AI.

1. Use AI Fairness 360 to Generate Bias Metrics
Generate bias metrics using AI Fairness 360 (AIF360). Here’s an example of computing bias metrics for a dataset:

python
Copy
Edit
from aif360.datasets import AdultDataset
from aif360.metrics import BinaryLabelDatasetMetric

# Load dataset
dataset = AdultDataset()

# Define privileged/unprivileged groups
privileged_groups = [{'sex': 1}]  # Example: Male
unprivileged_groups = [{'sex': 0}]  # Example: Female

# Compute bias metrics
metric = BinaryLabelDatasetMetric(dataset, privileged_groups, unprivileged_groups)

print("Statistical Parity Difference:", metric.statistical_parity_difference())
print("Disparate Impact:", metric.disparate_impact())
2. View Bias Metrics in ModelMesh Serving (if deployed)
If your model is deployed via ModelMesh Serving, you can log and monitor bias metrics as part of model monitoring.

Expose Metrics Using Prometheus: ModelMesh exposes metrics via an HTTP endpoint.
Set up Grafana Dashboards to visualize the fairness metrics.
Steps to View Bias Metrics:
Enable Prometheus Metrics Collection

Open OpenShift AI and navigate to Monitoring → Metrics.
Query model-serving metrics.
Use Grafana Dashboards

Navigate to Monitoring → Dashboards.
Create a panel to visualize bias metrics over time.
3. Bias Monitoring with OpenShift Pipelines
To automate bias detection, you can add a bias-checking step in your OpenShift Pipeline (Tekton) and store results in a database or monitoring tool. Example Tekton task:

yaml
Copy
Edit
steps:
  - name: check-bias
    image: python:3.9
    script: |
      pip install aif360 pandas
      python bias_analysis.py
4. View Bias Metrics via OpenShift AI Model Registry
If you store your model versions in OpenShift AI's Model Registry, you can:

Track fairness over different versions.
Store bias evaluation results as metadata.
Steps to View in Model Registry:
Navigate to Red Hat OpenShift AI → Model Registry.
Click on your model version.
Attach or upload bias metrics as metadata.
