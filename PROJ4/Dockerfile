FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --omit=dev
ARG REACT_APP_MOVIE_API_URL
ENV REACT_APP_MOVIE_API_URL=${REACT_APP_MOVIE_API_URL}
COPY . .
RUN npm run build || true
EXPOSE 8080
CMD ["npm","start"]