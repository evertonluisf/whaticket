# Stage de build
FROM node:18-alpine AS builder

# Instalar dependências de compilação e Python
RUN apk add --no-cache \
    python3 \
    make \
    g++ \
    build-base \
    bash

WORKDIR /app

# Copiar arquivos de configuração de dependências
COPY package*.json ./

# Limpar cache e instalar dependências de produção
RUN npm cache clean --force && \
    npm ci --only=production

# Copiar código-fonte
COPY . .

# Compilar assets (se necessário)
# RUN npm run build   # se houver etapa de build

# Stage final
FROM node:18-alpine

WORKDIR /app

# Instalar apenas runtime
RUN apk add --no-cache bash

# Copiar node_modules e build do stage anterior
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app ./

# Ajustar permissões (opcional)
RUN adduser -D whaticket && chown -R whaticket /app
USER whaticket

EXPOSE 3100

HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:3100/health || exit 1

CMD ["npm", "start"]
