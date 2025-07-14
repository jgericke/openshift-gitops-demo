FROM quay-ntpc5.apps.cluster-ntpc5.ntpc5.sandbox470.opentlc.com/quayadmin/python-alpine-golden:0.1 AS build

RUN pip install --no-cache-dir starlette uvicorn

FROM quay-ntpc5.apps.cluster-ntpc5.ntpc5.sandbox470.opentlc.com/quayadmin/python-alpine-golden:0.1 AS run

RUN adduser -S fritz -G root
USER fritz

COPY --from=build /usr/local/lib/python3.12/site-packages /usr/local/lib/python3.12/site-packages
COPY --chown=fritz:root main.py /home/fritz/main.py

EXPOSE 8080
WORKDIR /home/fritz
ENTRYPOINT ["python", "main.py"]
