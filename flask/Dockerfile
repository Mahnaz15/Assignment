FROM python:3.8-alpine
WORKDIR /app
COPY requirements.txt /app
RUN pip install -r requirements.txt
COPY db.py runserver.py /app
ADD notejam /app/notejam
RUN python db.py
EXPOSE 5000
ENTRYPOINT FLASK_APP=runserver.py flask run --host=0.0.0.0 

