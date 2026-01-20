# Base Image
FROM nginx:alpine

# Working directory
WORKDIR /usr/share/nginx/html

# Copy application files
COPY index.html .

# Expose port
EXPOSE 8081

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
