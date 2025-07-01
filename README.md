# SOFTDSNL - Activity 1: Python API Basics and Sci-kit Learn

## 🚀 Goals

- Understand how to set up a basic API using **Django Rest Framework**.
- Learn how to use **Scikit-Learn** to create a simple machine learning model.
- Deploy a basic API endpoint that accepts input and returns predictions.

---

## 🧠 What You'll Build

A simple API that predicts whether a flower is **Setosa**, **Versicolor**, or **Virginica** using the famous **Iris dataset**.

---

## 🔧 Requirements

- Python 3.10 or higher
- Basic understanding of Python
- Basic terminal/command line skills

---

## 📦 Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/rmanabat-hau/softdsnl-act1.git
cd softdsnl-act1
```

### 2. Create and activate a Virtual Environment (Skip this step if using HAU Lab PC. PC restrictions prevent creating VENV)

```bash
python -m venv venv
venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install django djangorestframework scikit-learn
```

---

## Project Structure

```
ml_api_project/
├── ml_api/
│   ├── migrations/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   ├── views.py          <-- (You will create this)
│   └── urls.py           <-- (You will create this)
├── ml_api_project/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py           <-- (You will modify this)
│   └── wsgi.py
├── manage.py
```

---

## 🛠️ Step-by-Step Instructions

### 1. Create Django Project and App

```bash
django-admin startproject ml_api_project .
python manage.py startapp ml_api
```

### 2. Configure Installed Apps

Open `ml_api_project/settings.py` and add the following to `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    # Default Django apps...
    'rest_framework',
    'ml_api',
]
```

---

### 3. Create the Machine Learning API View

Open `ml_api/views.py` and add the following code:

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

from sklearn.datasets import load_iris
from sklearn.ensemble import RandomForestClassifier
import numpy as np

# Load the Iris dataset and train the model when the server starts
iris = load_iris()
model = RandomForestClassifier()
model.fit(iris.data, iris.target)

class PredictIris(APIView):
    def post(self, request):
        try:
            data = request.data
            sepal_length = float(data.get('sepal_length'))
            sepal_width = float(data.get('sepal_width'))
            petal_length = float(data.get('petal_length'))
            petal_width = float(data.get('petal_width'))

            input_data = np.array([[sepal_length, sepal_width, petal_length, petal_width]])
            prediction = model.predict(input_data)
            predicted_class = iris.target_names[prediction][0]

            return Response({'prediction': predicted_class})
        except Exception as e:
            return Response({'error': str(e)}, status=status.HTTP_400_BAD_REQUEST)
```

---

### 4. Set Up URL Routing

#### Create `ml_api/urls.py`

```python
from django.urls import path
from .views import PredictIris

urlpatterns = [
    path('predict/', PredictIris.as_view(), name='predict-iris'),
]
```

#### Edit `ml_api_project/urls.py` to include the app routes

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('ml_api.urls')),
]
```

---

### 5. Run the Server

```bash
python manage.py runserver
```

The API will be accessible at:

```
http://127.0.0.1:8000/api/predict/
```

---

## 🔥 How to Test the API

You can use tools like **Postman**, **Insomnia**, or simply `curl`.

### Example Request

**POST** to:

```
http://127.0.0.1:8000/api/predict/
```

**Request Body (JSON):**

```json
{
  "sepal_length": 5.1,
  "sepal_width": 3.5,
  "petal_length": 1.4,
  "petal_width": 0.2
}
```

### Example Response

```json
{
  "prediction": "setosa"
}
```

---

## 🎯 Your Task

- Follow the steps to create the API.
- Test it with 10 different flower measurements.
- For each measurement, screenshot and provide the following:
  - The API URL
  - The request body
  - The response
- Organize the results in your report and pass it via Canvas. 

---

## 📚 Useful Resources

- [Django Rest Framework](https://www.django-rest-framework.org/)
- [Scikit-Learn Documentation](https://scikit-learn.org/stable/)
- [Iris Dataset Info](https://scikit-learn.org/stable/auto_examples/datasets/plot_iris_dataset.html)




