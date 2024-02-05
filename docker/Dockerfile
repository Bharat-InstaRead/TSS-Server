# Use an official NVIDIA base image with CUDA support
FROM nvidia/cuda:12.1.1-cudnn8-devel-ubuntu22.04

# Set label for the docker image description
LABEL description="Docker image for xtts-api-server"

# Install required packages (avoid cache to reduce image size)
RUN apt-get update && \
    apt-get install --no-install-recommends -y \
    python3-dev portaudio19-dev libportaudio2 libasound2-dev libportaudiocpp0 \
    git python3 python3-pip make g++ ffmpeg && \
    rm -rf /var/lib/apt/lists/* && \
    python3 -m pip install --upgrade pip setuptools wheel ninja virtualenv

# Create a virtual environment and activate it
RUN python3 -m venv venv
ENV PATH="/app/venv/bin:$PATH"

# Copy the application source code to /app directory and change the workdir to /app
COPY . /app
WORKDIR /app

# Install Python dependencies
RUN pip install torch torchaudio --index-url https://download.pytorch.org/whl/cu121
RUN pip install deepspeed
RUN pip install xtts-api-server

# Expose the container ports
EXPOSE 8020

# Set environment variables
ENV LISTEN_PORT=8020 \
    API_URL='http://localhost:8020' \
    SPEAKERS_FOLDER='xtts-server/speakers' \
    OUTPUT_FOLDER='xtts-server/output' \
    MODELS_FOLDER='xtts-server/models'

# Run xtts_api_server when the container starts
CMD ["python3", "-m", "xtts_api_server", "--listen", "-p", "${LISTEN_PORT}", "-t", "${API_URL}", "-sf", "${SPEAKERS_FOLDER}", "-o", "${OUTPUT_FOLDER}", "-mf", "${MODELS_FOLDER}", "--deepspeed"]
