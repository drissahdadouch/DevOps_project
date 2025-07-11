FROM node:16 AS builder

WORKDIR /app

COPY package*.json ./

RUN npm install --force

COPY . .

FROM node:16

WORKDIR /app

COPY --from=builder /app .

RUN npm install --only=production --force
EXPOSE 8000
CMD ["npm", "start"]
