FROM php:8.1-fpm

ARG USERNAME="ravuthz"
ARG USER_UID="1000"
ARG USER_GID=$USER_UID

# Arguments defined in docker-compose.yml
ARG NODE_VERSION="none"

# * MS - https://code.visualstudio.com/remote/advancedcontainers/add-nonroot-user *
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    #
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    #
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# RUN useradd -G www-data,root -u $USER_UID -d /home/$USERNAME $USERNAME
# RUN mkdir -p /home/$USERNAME/.composer && \
#     chown -R $USERNAME:$USERNAME /home/$USERNAME

# ********************************************************
# * Anything else you want to do like clean up goes here *
# ********************************************************

# [Optional] Set the default user. Omit if you want to keep the default as root.
# USER $USERNAME

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd

# Install PostgreSQL PDO
# RUN apt-get update && apt-get install -y libpq-dev && docker-php-ext-install pdo pdo_pgsql

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Create some alias easy to create new laravel app
RUN echo 'alias laravel_new="composer create-project laravel/laravel"' >> ~/.bashrc

RUN if [ "${NODE_VERSION}" != "none" ]; then \
        curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash \
        && . ~/.nvm/nvm.sh \
        && nvm install $NODE_VERSION 2>&1 \
        && nvm alias default $NODE_VERSION \
        && nvm use default; \
    fi


USER $USERNAME
