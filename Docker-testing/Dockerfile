FROM public.ecr.aws/lambda/python:3.9

# Copy function code
COPY app.py ${LAMBDA_TASK_ROOT}

# Set the handler (filename.function_name)
CMD ["app.handler"]
