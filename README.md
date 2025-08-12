<<<<<<< HEAD
This is the README for Visual Path App project.
=======
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
>>>>>>> b0a70ebf5a0f72ec22f5b63ab3c04795a3fd59ee
