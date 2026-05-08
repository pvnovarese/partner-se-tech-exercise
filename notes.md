# changes

## 1) cgr images
    * postgres in the compose file is straightforward swapout
    * for the app/Dockerfile, python is more involved, the chainguard python image is stripped down so I need a multi-stage build with the python:latest-dev image
    * python:latest-dev image needs postgresql-dev added
    * pinned all of these to a digest

## 2) libraries
    * first got all the hashes with "pip-compile --generate-hashes requirements.in"
    * changes to app/Dockerfile:
        * --mount to get chainguard library credentials from .env
        * --require-hashes to pin dependencies
        * --index-url to try chainguard libraries first
        * --extra-index-url to fall back to pypi for anything not in the chainguard package registry

## 3) general improvements:
    * changed app/Dockerfile entrypoint directly to app.py instead of entrypoint.sh
    * added healthcheck to db service, depends_on to web service to make sure the db is up before app starts
    * removed hardcoded postgres user/password/secrets into .env file
    * add .dockerignore and .gitignore so .env doesn't get copied into app image or git repo
    * moved app volume mount in web service to an override compose file for devs
    * added explicit bridge network "backend"
    * pin python dependencies
        mv requirements.txt requirements.in
        pip-compile --generate-hashes requirements.in
        (add --require-hashes to pip install in app/Dockerfile)
    * FLASK_ENV=development is deprecated and dangerous in production, not really needed here, set FLASK_DEBUG=0 (could be changed to 1 for debugging)
    * removed deprecated version element from docker-compose.yml
    * added name element to docker-compose.yml (not really needed)
    * add HEALTHCHECK to app/Dockerfile (a little superfluous maybe)
