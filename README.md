# nuxt-ecs-fargate

## Build and push the app image

```sh
$ yarn create nuxt-app nuxt-ecs-fargate
? Project name: nuxt-ecs-fargate
? Programming language: JavaScript
? Package manager: Yarn
? UI framework: None
? Nuxt.js modules: (Press <space> to select, <a> to toggle all, <i> to invert selection)
? Linting tools: (Press <space> to select, <a> to toggle all, <i> to invert selection)
? Testing framework: None
? Rendering mode: Universal (SSR / SSG)
? Deployment target: Server (Node.js hosting)
? Development tools: (Press <space> to select, <a> to toggle all, <i> to invert selection)
? What is your GitHub username? shinya fujino
? Version control system: Git
$ cd nuxt-ecs-fargate
$ vim package.json # Add "--hostname '0'" to the start command
$ cat package.json
cat package.json
{
  "name": "nuxt-ecs-fargate",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "nuxt",
    "build": "nuxt build",
    "start": "nuxt start --hostname '0'",
    "generate": "nuxt generate"
  },
  "dependencies": {
    "core-js": "^3.8.3",
    "nuxt": "^2.14.12"
  },
  "devDependencies": {}
}
$ vim Dockerfile
$ cat Dockerfile
FROM node:14-alpine

WORKDIR /app

COPY . .

RUN yarn --frozen-lockfile

RUN yarn build

EXPOSE 3000

CMD ["yarn", "start"]
$ docker build -t nuxt-ecs-fargate .
$ aws ecr get-login-password --region ap-northeast-1 | docker login --username AWS --password-stdin aws_account_id.dkr.ecr.region.amazonaws.com # Optional: Authenticate Docker to your registry
$ aws ecr create-repository \
    --repository-name nuxt-ecs-fargate \
    --image-scanning-configuration scanOnPush=true \
    --region ap-northeast-1
$ docker tag nuxt-ecs-fargate:latest aws_account_id.dkr.ecr.ap-northeast-1.amazonaws.com/nuxt-ecs-fargate:latest
$ docker push aws_account_id.dkr.ecr.ap-northeast-1.amazonaws.com/nuxt-ecs-fargate:latest
```

## TODO: Deploy the app

## References

- [Using Amazon ECR with the AWS CLI](https://docs.aws.amazon.com/AmazonECR/latest/userguide/getting-started-cli.html)
