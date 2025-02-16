FROM python:3.12

WORKDIR /app

RUN apt-get update && apt-get install -y \
gettext \
libffi8 \
libgdk-pixbuf2.0-0 \
liblcms2-2 \
libopenjp2-7 \
libssl3 \
libtiff6 \
libwebp7 \
shared-mime-info \
mime-support \
libpq5 \
&& apt-get clean && rm -rf /var/lib/apt/lists/*

RUN pip install poetry==1.8.4

RUN poetry config virtualenvs.create false

COPY pyproject.toml poetry.lock ./

RUN poetry install --no-root

RUN groupadd -r saleor  && useradd -r -g saleor saleor 
RUN chown -R saleor:saleor /app/

USER saleor

COPY --chown=saleor:saleor . /app/

ARG STATIC_URL
ENV STATIC_URL=${STATIC_URL:-/static/}
RUN SECRET_KEY=dummy STATIC_URL=${STATIC_URL} python3 manage.py collectstatic --no-input

EXPOSE 8000

CMD ["gunicorn", "--bind", ":8000", "--workers", "4", "--worker-class", "saleor.asgi.gunicorn_worker.UvicornWorker", "saleor.asgi:application"]