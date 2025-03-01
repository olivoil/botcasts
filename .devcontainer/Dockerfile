FROM ruby:3.3.0

# Install common dependencies including libvips and Node.js for importmaps
RUN apt-get update -qq && \
    apt-get install -y build-essential libpq-dev nodejs git \
    libvips libvips-dev libvips-tools && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Create a non-root user
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# Set working directory
WORKDIR /workspace

# Copy the ruby version file first
COPY .ruby-version ./

# Then copy gemfiles for installation
COPY Gemfile Gemfile.lock ./

# Install gems as root
RUN bundle install

# Switch to non-root user
USER $USERNAME

# Set PATH for gems installed by this user
ENV PATH="/home/$USERNAME/.local/bin:${PATH}"
ENV GEM_HOME="/home/$USERNAME/.gems"
ENV GEM_PATH="/home/$USERNAME/.gems"
ENV PATH="/home/$USERNAME/.gems/bin:${PATH}"

# Install common gems
RUN gem install solargraph rubocop debug

# Expose ports
EXPOSE 3000