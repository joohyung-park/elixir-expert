FROM elixir:latest

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    wget \
    tree \
    && rm -rf /var/lib/apt/lists/*
 # Install Just
RUN wget -qO- https://github.com/casey/just/releases/download/1.36.0/just-1.36.0-x86_64-unknown-linux-musl.tar.gz | tar xvz && \
    mv just /usr/local/bin/ && \
    chmod +x /usr/local/bin/just
 # Install Zig 0.14.1
RUN cd /opt && \
    wget https://ziglang.org/download/0.14.1/zig-x86_64-linux-0.14.1.tar.xz && \
    tar -xJf zig-x86_64-linux-0.14.1.tar.xz && \
    mv zig-x86_64-linux-0.14.1 zig && \
    rm zig-x86_64-linux-0.14.1.tar.xz && \
    ln -s /opt/zig/zig /usr/local/bin/zig
 # Install 7zip
RUN wget https://www.7-zip.org/a/7z2501-linux-x64.tar.xz && \
    tar -xJf 7z2501-linux-x64.tar.xz && \
    mv 7zz /usr/local/bin/ && \
    rm 7z2501-linux-x64.tar.xz *.txt && \
    rm -rf MANUAL/
 # Clone and build expert
WORKDIR /opt
RUN git clone https://github.com/elixir-lang/expert && \
    cd expert && \
    just deps forge && \
    just deps engine && \
    just deps expert 


# Build expert with retry logic
WORKDIR /opt/expert
RUN just release-local || { \
    echo "First build failed, retrying..."; \
    sleep 2; \
    just release-local; \
    }

# Make expert binary readable and executable for all users
RUN chmod 755 /opt/expert/apps/expert/burrito_out/expert_linux_amd64

 # Add expert to PATH
ENV PATH="${PATH}:/opt/expert/apps/expert/_build/prod/rel/expert/bin"
 # devcontainer will handle user setup
WORKDIR /workspace
CMD ["/bin/bash"]
