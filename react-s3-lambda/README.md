# react-s3-lambda

![arch image for nerds](https://github.com/OpeOginni/interesting-deployments/blob/main/react-s3-lambda/media/react-s3-lambda.png)

## S3 Bucket Config

1) After building the static frontend `bun run build`, copy every file and place in a bucket.

2) Give the property `Static website hosting` to be enabled.

3) Set the Block all Public Access to `false`

4) Provide a policy that allows the objects int he bucket to be fetched publicly.
```json
    {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicRead",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::{bucket_name}/*"
        }
    ]
}
```
## Lambda Creation

1) Create lambda for both api endpoints `/api/hello` and `/api/hello/:name`

```mjs
// Lambda for /api/hello

export const handler = async (event) => {
  const response = {
    statusCode: 200,
    body: JSON.stringify({ 
      message: 'Hello from Lambda!',
      method: event.requestContext.http.method
    }),
  };
  return response;
};
```

```mjs
// Lambda for /api/hello/:name

export const handler = async (event) => {
  const name = event.pathParameters.proxy;
  const response = {
    statusCode: 200,
    body: JSON.stringify({
      message: `Hello, ${name}!`,
      method: event.requestContext.http.method
    }),
  };
  return response;
};
```
2) Give them cool names IG, that was mostly it for the lambda

## Creating the API Gateway

1) Create a HTTP API gateway endpoint

2) Add the Lambda instances as Integrations

3) Add routes for the Lambda instances
    - Tip for dynamic path names use the `{proxy+}` for the path. For example `/api/hello/:name` -> `/api/hello/{proxy+}`

## Creating CloudFront Distribution

1) Create a CloudFront distribution with the S3 Bucket holding the Static Built Frontend.

2) Add in the API Gateway as an origin for the cloudfront distribution.

3) Add proper behaviours making sure that only requires with `/api/*` goes to the API Gateway

