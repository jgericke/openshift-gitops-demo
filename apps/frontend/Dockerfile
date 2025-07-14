FROM quay-ntpc5.apps.cluster-ntpc5.ntpc5.sandbox470.opentlc.com/quayadmin/python-alpine-golden:0.1 AS build

# Install Bash and other dependencies
RUN apk add --no-cache bash

# Install required Python packages
RUN pip install --no-cache-dir httpx psycopg psycopg_binary psycopg_pool starlette sse_starlette uvicorn

# Install vulnerable packages for testing
RUN pip install --no-cache-dir setuptools==39.1.0
RUN pip install --no-cache-dir Flask==0.5
RUN pip install --no-cache-dir Django==1.11.29
RUN pip install --no-cache-dir requests==2.19.0
RUN pip install --no-cache-dir PyYAML==3.12

FROM quay-ntpc5.apps.cluster-ntpc5.ntpc5.sandbox470.opentlc.com/quayadmin/python-alpine-golden:0.1 AS run

# Install Bash in the runtime container
RUN apk add --no-cache bash

# Create user and set permissions
RUN adduser -S fritz -G root
USER fritz

# Copy files from the build stage
COPY --from=build /usr/local/lib/python3.12/site-packages /usr/local/lib/python3.12/site-packages
COPY --chown=fritz:root static /home/fritz/static
COPY --chown=fritz:root main.py /home/fritz/main.py

# Expose the port and set the working directory
EXPOSE 8080
WORKDIR /home/fritz

# Set the entry point to start the application
ENTRYPOINT ["python", "main.py"]
