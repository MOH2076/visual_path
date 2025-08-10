# Use Python base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy app files
COPY app/requirements.txt requirements.txt
RUN pip install -r requirements.txt

COPY app/ .

# Run the app
CMD ["python", "app.py"]
