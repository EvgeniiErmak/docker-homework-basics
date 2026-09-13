FROM redmine:5-alpine

RUN apk add --no-cache git && \
    git clone --depth 1 https://github.com/farend/redmine_theme_farend_bleuclair.git \
        /usr/src/redmine/public/themes/farend_bleuclair && \
    rm -rf /usr/src/redmine/public/themes/farend_bleuclair/.git
