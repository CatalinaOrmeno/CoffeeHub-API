# --- ETAPA 1: Compilación de los archivos estáticos ---
FROM node:20-alpine AS builder
LABEL authors="catalina"

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# --- ETAPA 2: Servidor web ligero para producción ---
FROM nginx:1.25-alpine

COPY --from=builder /app/dist /usr/share/nginx/html

COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]