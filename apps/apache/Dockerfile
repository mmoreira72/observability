FROM php:7.4-apache

# Install required packages
RUN apt-get update && apt-get install -y \
    apache2-utils \
    wget \
    tar \
 && a2enmod status \
 && rm -rf /var/lib/apt/lists/*

# Enable mod_status for Apache
COPY apache.conf /etc/apache2/conf-enabled/server-status.conf

# Download and install Apache Exporter (x86_64 binary)
RUN wget https://github.com/Lusitaniae/apache_exporter/releases/download/v1.0.9/apache_exporter-1.0.9.linux-amd64.tar.gz -O /tmp/apache_exporter.tar.gz && \
    tar -xzf /tmp/apache_exporter.tar.gz -C /tmp && \
    mv /tmp/apache_exporter-1.0.9.linux-amd64/apache_exporter /usr/local/bin/apache_exporter && \
    chmod +x /usr/local/bin/apache_exporter && \
    rm -rf /tmp/apache_exporter*

# Copy application files (optional)
COPY ./app /var/www/html

# Expose Ports
EXPOSE 80 9117

# Start both Apache and Exporter in the same container
CMD ["sh", "-c", "apache2-foreground & /usr/local/bin/apache_exporter --scrape_uri=http://localhost/server-status?auto"]
