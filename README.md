# docker-oci
Local docker setup for a project requiring oci8 library (to work with Oracle DB)

1. Clone and configure needed project inside this directory (in this setup, project arb-platform)
2. Install docker and docker-compose https://docs.docker.com/compose/install/
3. Run `docker-compose up -d`
4. Make installations for project: `docker exec -it dockeroci_php-fpm_1 composer install`
5. Check localhost:8078