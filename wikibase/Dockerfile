FROM ubuntu as fetcher

RUN apt-get update && \
    apt-get install --yes --no-install-recommends ca-certificates wget unzip git

# Creates /mediawiki-extensions-Wikibase-REL1_29
RUN wget https://github.com/wikimedia/mediawiki-extensions-Wikibase/archive/REL1_29.zip &&\
    unzip REL1_29.zip

RUN git clone https://github.com/filbertkm/WikibaseImport.git /WikibaseImport && \
    cd /WikibaseImport && \
    git checkout d9705f1509edec4cdc2a6fad3f6bfec6bf130e3d && \
    rm -rf /WikibaseImport/.git


FROM composer as composer

COPY --from=fetcher /mediawiki-extensions-Wikibase-REL1_29 /Wikibase
COPY --from=fetcher /WikibaseImport /WikibaseImport

RUN cd /Wikibase && composer install --no-dev && \
    cd /WikibaseImport && composer install --no-dev


FROM mediawiki:1.29

RUN a2enmod rewrite

COPY --from=composer /Wikibase /var/www/html/extensions/Wikibase
COPY --from=composer /WikibaseImport /var/www/html/extensions/WikibaseImport

COPY wait-for-it.sh /wait-for-it.sh
COPY entrypoint.sh /entrypoint.sh
COPY LocalSettings.php /LocalSettings.php
COPY htaccess /var/www/html/.htaccess

RUN ln -s /var/www/html/ /var/www/html/w

ENTRYPOINT ["/bin/sh"]
CMD ["/entrypoint.sh"]