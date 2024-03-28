VERSION 0.8
# Howto clone, commit and push with earthly
# To test this example:
#  1. clone this repository
#  2. create or check that it exists a branch gh-pages in it
#  3. create a token on github and 
#  4. use the following command:
# earthly +push-gh-pages --GIT_CREDENTIALS=your_token --GIT_REPO_URL=github.com/[REPO-OWNER]/[REPO-NAME] --GIT_USER_EMAIL=your_email --GIT_USER_NAME=your_name
# In a debug context, a local repository should be used instead of the remote one

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
    FROM +deps
    # Make sure to securely handle your GITHUB_TOKEN and REPO_URL
    ARG GIT_CREDENTIALS
    ARG GIT_REPO_URL
    ARG GIT_USER_EMAIL
    ARG GIT_USER_NAME
    #First clone gh-pages branch into gh-pages directory
    # if the branch gh-pages exists otherwise create it
    RUN --no-cache EXIST=$(git clone --branch gh-pages --single-branch https://$GIT_CREDENTIALS@$GIT_REPO_URL gh-pages && echo "YES" || echo "NO") && \
        if [ "$EXIST" = "YES" ]; \
            then echo "Documentation branch gh-pages exists, updating it"; \
        else \
            echo "Documentation branch gh-pages does not exist, creating it" && \
            git clone https://$GIT_CREDENTIALS@$GIT_REPO_URL gh-pages && \
            cd gh-pages && git checkout -b gh-pages && \
            git rm -rf . && \
            cd ..; \
        fi
    # copy the documentation files to the gh-pages directory
    # COPY --dir +build_doc/docs/* gh-pages/
    ##### JUST TO MAKE OUR TEST CREATE NEW CONTENT EACH TIME
    RUN touch gh-pages/new_change.txt
    RUN echo "That is what we can do best at " >> gh-pages/new_change.txt
    RUN date -Ins -u >> gh-pages/new_change.txt
    ##### end : JUST TO MAKE OUR TEST CREATE NEW CONTENT EACH TIME
    RUN cd gh-pages && \
        git config --local user.email $GIT_USER_EMAIL && \
        git config --local user.name $GIT_USER_NAME && \
        git add . && \
        git commit -m "Update documentation" -a || true && \
        git push origin gh-pages

# Push to gh-pages branch but nothing to commit what happens ?
push-gh-pages-no-commit:
    FROM +deps
    # Make sure to securely handle your GITHUB_TOKEN and REPO_URL
    ARG GIT_CREDENTIALS
    ARG GIT_REPO_URL
    ARG GIT_USER_EMAIL
    ARG GIT_USER_NAME
    #First clone gh-pages branch into gh-pages directory
    # if the branch gh-pages exists otherwise create it
    RUN --no-cache EXIST=$(git clone --branch gh-pages --single-branch https://$GIT_CREDENTIALS@$GIT_REPO_URL gh-pages && echo "YES" || echo "NO") && \
        if [ "$EXIST" = "YES" ]; \
            then echo "Documentation branch gh-pages exists, updating it"; \
        else \
            echo "Documentation branch gh-pages does not exist, creating it" && \
            git clone https://$GIT_CREDENTIALS@$GIT_REPO_URL gh-pages && \
            cd gh-pages && git checkout -b gh-pages && \
            git rm -rf . && \
            cd ..; \
        fi
    # copy the documentation files to the gh-pages directory
    # COPY --dir +build_doc/docs/* gh-pages/
    ##### NOTHING TO COMMIT #####
    #############################
    RUN cd gh-pages && \
        git config --local user.email $GIT_USER_EMAIL && \
        git config --local user.name $GIT_USER_NAME && \
        git add . && \
        git commit -m "Update documentation" -a || true && \
        git push origin gh-pages
