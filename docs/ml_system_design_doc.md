# ML System Design Document: Resume-Based Salary Prediction System

## 1. Project Motivation
The salary prediction system aims to provide jobseekers with accurate salary expectations based on their resumes, helping them make informed career decisions and negotiate better compensation packages. This addresses the common challenge of salary opacity in the job market and empowers users with data-driven insights about their market value.

## 2. Business Requirements and Constraints

### 2.1 Business Requirements
- Provide accurate salary predictions to ensure reliability for job seekers
- Generate interactive visualizations showing the user's position in the job market landscape
- Process and analyze resumes in real-time (response time < 30 seconds)
- Handle both Russian and English language resumes
- Support resume uploads in PDF format

### 2.2 Business Constraints
- Must comply with Russian personal data protection laws
- Maximum processing cost per resume should not exceed $0.01
- System should handle up to 1000 concurrent users
- Must maintain user privacy and data confidentiality

### 2.3 Problem Solving Steps

#### Stage 1. Data Preparation

**Key results from EDA:**
- Dataset contains 19489 records with minimal missing values (only in tags_id)
- Salary distributions are left-skewed with most values under 100,000 RUB
- Data primarily represents "blue collar" positions and retail/sales roles
- Strong geographical concentration in major cities (Moscow, St. Petersburg)

**Data Quality Issues:**
1. Imbalanced position distribution:
   - Top positions have 400+ entries
   - Many positions have very few entries
2. Salary range variability:
   - Wide gaps between salary_from and salary_to
   - Need to handle outliers (especially in high salary ranges)
3. Skills data requires preprocessing:
   - Currently stored as string representations of lists
   - Mix of hard and soft skills
   - Varying granularity of skill descriptions

**Data Generation Process:**
- Data appears to be from job posting platform
- Regular updates expected as new vacancies are posted
- One-time data unload for now

**Required Stage Result:**
Clean, preprocessed dataset with:
- Normalized position names
- Removed outliers from salaries
- Processed skills features


#### Stage 2. Preparation of Predictive Models

##### ML Metrics Selection
Primary metrics:
- MAE (Mean Absolute Error) - for absolute error in salary predictions
- MAPE (Mean Absolute Percentage Error) - interpretable for salary ranges
- R^2 score - to measure overall prediction quality

Validation scheme:
- Train-test split (80/20) to simulate real-world prediction scenario
- K-fold cross-validation (k=5) for model stability assessment

##### Baseline models
1. Simple baseline:
   - Linear Regression / KNN using basic features:
     - City id
     - Schedule type
     - Custom position
   - Expected MAE: 30k-40k

2. Advanced baseline:
   - Gradient boosting (LightGBM/CatBoost)
   - Additional features:
     - Position embeddings
     - Skills embeddings
     - Salary normalization
   - Clustering on positions and skills (K-means?)
   - Expected MAE: <20k

##### Development Strategy
1. Feature Engineering:
   - Text embeddings for position names
   - Skills clustering and aggregation

2. Model Selection:
   - Compare tree-based models (LightGBM, CatBoost)
   - Ensemble methods for stability

##### Risks and Mitigation
1. Data Risks:
   - Salary range variability - use outlier handling
   - Skills / position bias - develop cluster-specific models

2. Model Risks:
   - Skill importance stability - regular feature importance analysis
   - Model drift - implement monitoring and retraining pipeline

**Required Stage Result:**
- Validated baseline models meeting minimum performance criteria
- Feature importance analysis
- Model interpretation reports

#### Stage 3. Market Segmentation and Clustering

##### Approach
- Implement clustering algorithms for position/skills segmentation
- Define similarity metrics for skills and positions
- Create interactive visualizations for market positioning

##### Methods
- K-means clustering for initial segmentation
- Dimension reduction for visualization

##### Required Stage Result
- Defined optimum number of clusters
- Visualization system for user positioning
- Documentation of clustering methodology

#### Stage 4. System Integration and Deployment

##### Integration Steps
- API development for model serving
- Frontend integration with visualization tools
- Monitoring system setup
- Performance optimization

##### Success Criteria
- System stability under load
- Proper error handling and logging

##### Required Stage Result
- Deployed system meeting all technical requirements
- Documentation for maintenance and updates
- Monitoring dashboard for system health

## 3. Project Scope

### 3.1 In Scope
- Resume text extraction and processing pipeline
- Salary prediction and job market insights
- Segmentation of users based on skills and positions
- MVP implementation with web interface
- Basic error handling and input validation
- Model monitoring and retraining pipeline

### 3.2 Out of Scope
- Integration with external job boards
- Salary negotiation recommendations
- Career path suggestions
- Resume improvement recommendations
- Other services (mobile app, telegram bot, etc.)

## 4. Solution Prerequisites
- Historical salary data (minimum 1000 records)
- Tools for text analysis and embedding generation for multi-language support
- Computing resources for model training and inference
- Storage infrastructure for user data and model artifacts

## 5. Task Definition

### 5.1 Machine Learning Tasks
1. **Regression Task**
   - Input: Extracted resume text features and relevant embeddings
   - Output: Predicted salary range
   - Success criteria: To be determined based on data analysis and business requirements

2. **Clustering Task**
   - Input: Processed position and skills data
   - Output: Segmented groups representing job market clusters
   - Success criteria: To be determined based on data analysis and business requirements

### 5.2 Technical Requirements
- Model inference time: < 30 seconds
- API response time: < 10 seconds
- System uptime: 99.9%
- Maximum file size for uploads: 100MB

## 6. Solution Architecture
### System Overview
The whole system consists of the following components

1. **Frontend Layer**

   A web-based interface for user interactions (e.g., resume uploads, results display, and feedback collection).

2. **Processing Layer**

   Methods for transforming resume data (after text extraction and preprocessing) into usable features for downstream tasks.

3. **Model Layer**
   - Predictive models for salary
   - Analytical processes for skills and position clustering
   - Model registry for managing versions and monitoring performance.

4. **Data Layer**
   - Storage for resumes, historical salary data, and user feedback.
   - Management of processed outputs and model artifacts.

### Conceptual Data Flow
1. The user uploads a resume through the web interface.
2. The system extracts and preprocesses relevant information from the resume (e.g., skills, experience, and education).
3. Embedding methods are applied to transform extracted features into numerical representations.
4. Predictive and clustering methods are applied to generate salary insights and find corresponing cluster for the user in the job market.
5. Results are displayed through interactive dashboards.

```mermaid
graph TD
A[User resume upload] --> B[Text extraction and processing]
    B --> C[Feature transformation]
    C --> D[Predictive analysis]
    C --> E[Clustering analysis]
    D --> F[Salary insights]
    E --> G[Skills and position Insights]
    F --> H[Results dashboard display]
    G --> H
```

### Model training pipeline:
```mermaid
graph TD
    A[Data collection] --> B[Data processing]
    B --> C[Feature engineering]
    C --> D[Model development]
    D --> E[Model evaluation]
    E --> F[System integration]
    F --> G[Deployment]
    G --> H[Monitoring and feedback]  --> A
```