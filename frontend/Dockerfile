# Stage 1: Build Angular
FROM node:20 AS build
WORKDIR /app

# Copiar package.json e package-lock.json
COPY package*.json ./

# Instalar dependências
RUN npm install

# Copiar código-fonte
COPY . .

# Build para produção
RUN npm run build

# Stage 2: Servir com Nginx
FROM nginx:alpine
WORKDIR /usr/share/nginx/html

# Copiar arquivos compilados do estágio anterior
COPY --from=build /app/dist/frontend /usr/share/nginx/html

# Copiar configuração nginx customizada
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Expor porta
EXPOSE 8080

# Comando padrão
CMD ["nginx", "-g", "daemon off;"]
