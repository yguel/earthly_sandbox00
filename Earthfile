VERSION 0.8
# Howto clone, commit and push with earthly
# To test this example:
#  1. clone this repository
#  2. create or check that it exists a branch gh-pages in it
#  3. create a token on github and use the following command:
# earthly +push-gh-pages --GIT_CREDENTIALS=your_token --GIT_REPO_URL=github.com/[REPO-OWNER]/[REPO-NAME] --GIT_USER_EMAIL=your_email --GIT_USER_NAME=your_name

FROM ubuntu:latest
WORKDIR /earthly_workdir

deps:
    RUN apt-get update && apt-get install -y git

create-new-file:
    FROM +deps
    RUN touch new_change.txt
    RUN echo "That is what we can do best at " >> new_change.txt
    RUN date -Ins -u >> new_change.txt 
    SAVE ARTIFACT ./new_change.txt

# Push to gh-pages branch
push-gh-pages:
    FROM +create-new-file
     # Make sure to securely handle your GITHUB_TOKEN and REPO_URL
    ARG GIT_CREDENTIALS
    ARG GIT_REPO_URL
    ARG GIT_USER_EMAIL
    ARG GIT_USER_NAME
    #First clone into gh-pages directory
    RUN git clone --branch gh-pages --single-branch https://$GIT_CREDENTIALS@$GIT_REPO_URL gh-pages
    # copy the new_change.txt file
    COPY --dir +create-new-file/new_change.txt gh-pages/
    RUN cd gh-pages && \
        git config --local user.email $GIT_USER_EMAIL && \
        git config --local user.name $GIT_USER_NAME && \
        git add . && \
        git commit -m "Update documentation" -a || true && \
        git push origin gh-pages
