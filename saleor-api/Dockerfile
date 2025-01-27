FROM python:3.12

WORKDIR /app

RUN apt-get update\
&& apt-get install\
libffi8\
libgdk-pixbuf2.0-0\
liblcms2-2\
libopenjp2-7\
libssl3\
libtiff6\
libwebp7\
shared-mime-info\
mime-support\
libpq5\
&& apt-get clean

RUN pip install poetry==1.8.4

COPY pyproject.toml poetry.lock /app/

RUN poetry install --no-root

RUN groupadd -r saleor  && useradd -r -g saleor saleor 

RUN chown -R saleor:saleor /app/

ARG STATIC_URL
ENV STATIC_URL=${STATIC_URL:-/static/}
RUN SECRET_KEY=dummy STATIC_URL=${STATIC_URL} python3 manage.py collectstatic --no-input

#COPY . .

EXPOSE 8000

#ENTRYPOINT [ "/app/start.sh" ]

CMD ["gunicorn", "--bind", ":8000", "--workers", "4", "--worker-class", "saleor.asgi.gunicorn_worker.UvicornWorker", "saleor.asgi:application"]