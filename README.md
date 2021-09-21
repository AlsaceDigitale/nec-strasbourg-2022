# Scalingo 12-Factor WordPress Distribution

## Features

Based on [Bedrock](https://roots.io/bedrock/)

* Better folder structure
* Dependency management with [Composer](http://getcomposer.org) and [WordPress Packagist](https://wpackagist.org/)
* Easy WordPress configuration with environment specific files
* Environment variables with [Dotenv](https://github.com/vlucas/phpdotenv)
* Autoloader for mu-plugins (use regular plugins as mu-plugins)
* Enhanced security (separated web root and secure passwords with [wp-password-bcrypt](https://github.com/roots/wp-password-bcrypt))

With few more features added by `Scalingo`:

* Configurable from var environment
* File Uploads sent to S3 Bucket by default with [S3-Uploads plugin](https://github.com/humanmade/S3-Uploads)

Actual WordPress version : `5.5`

## Installation

The variables `S3_UPLOADS_BUCKET`, `S3_UPLOADS_KEY`, `S3_UPLOADS_SECRET`, `S3_UPLOADS_REGION`
are not required when you don't want to use the WordPress image upload with S3.

In reverse, if you activate the already preinstalled S3 plugin you must specify these variables.

### One-click installation

[![Deploy to Scalingo](https://cdn.scalingo.com/deploy/button.svg)](https://my.scalingo.com/deploy?source=https://github.com/Scalingo/scalingo-wordpress)

### Manual installation

1. Clone this distribution

```bash
git clone https://github.com/Scalingo/scalingo-wordpress
cd scalingo-wordpress
```

2. Use Scalingo CLI for create the `app` and add `mysql addon`

```bash
scalingo create my-wordpress
scalingo addons-add mysql mysql-sandbox
```

3. Create an S3 Bucket on AWS and configure IAM user correctly (only if you want to use image upload with S3)

IAM user security policy example:
```bash
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "s3:PutObject",
        "s3:PutObjectAcl",
        "s3:PutObjectAclVersion",
        "s3:AbortMultipartUpload",
        "s3:ListBucket",
        "s3:GetObject"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::bucketname-here"
    },
    {
      "Action": [
        "s3:PutObject",
        "s3:PutObjectAcl",
        "s3:PutObjectAclVersion",
        "s3:AbortMultipartUpload",
        "s3:ListBucket",
        "s3:GetObject"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::bucketname-here/*"
    }
  ]
}
```

3. Update the application environment through the dashboard or with the
   [Scalingo command line](http://cli.scalingo.com) `scalingo env-set VARIABLE_NAME=VALUE`

  * `DATABASE_URL` - Connection string to the MySQL database - `mysql://localhost:3306/wp-bedrock` - Automatically added with the Scalingo MySQL addon
  * `WP_ENV` - Set to environment (`development`, `staging`, `production`) (required)
  * `WP_HOME` - Full URL to WordPress home (http://my-wordpress.scalingo.io) (required)
  * `WP_SITEURL` - Full URL to WordPress including subdirectory (http://my-wordpress.scalingo.io/wp) (required)
  * `S3_UPLOADS_BUCKET` - Name of the S3 bucket to upload files to (optional)
  * `S3_UPLOADS_KEY` - AWS Access Key ID for S3 authentication (optional)
  * `S3_UPLOADS_SECRET` - AWS Secret Key for S3 authentication (optional)
  * `S3_UPLOADS_REGION` - Region of the S3 bucket (optional)
  * `AUTH_KEY`, `SECURE_AUTH_KEY`, `LOGGED_IN_KEY`, `NONCE_KEY`, `AUTH_SALT`, `SECURE_AUTH_SALT`, `LOGGED_IN_SALT`, `NONCE_SALT`

  You can get some random salts on the [Roots WordPress Salt Generator][roots-wp-salt].

4. Add theme(s) in `web/app/themes` as you would for a normal WordPress site.

5. Deploy the application on Scalingo

```bash
# Optionally add theme to your git repository
git add web/app/themes
git commit -m "Add themes"

# Then push to Scalingo
git push scalingo master
```

6. Access WP admin at `https://my-wordpress.scalingo.io/wp/wp-admin`

7. Activate `S3 Uploads` plugin on WordPress plugin page at `https://my-wordpress.scalingo.io/wp/wp-admin/plugins.php`

## Use in Development

### Requirements

* [Docker](https://docs.docker.com/install/)
* [Docker Compose](https://docs.docker.com/compose/install/)

### Updating WordPress version

Update package.json to update the WordPress branch you need.

Then run:

```
└> docker-compose run --rm web composer update
```

Run locally to test WordPress is working, then commit `composer.json` and `composer.lock`.

### Run locally

A Docker Compose file is available to run the WordPress locally. You first need
to install the dependencies with:

```bash
docker-compose run --rm composer install --prefer-source --no-interaction --ignore-platform-reqs
```

Then start the Nginx:

```bash
docker-compose up nginx
```

#### Install Wordpress Plugin

Simple command for install plugins:
```bash
docker-compose run --rm composer require --ignore-platform-reqs wpackagist-plugin/{PLUGIN_NAME}
```

You can find `plugins` on [Wordpress Packagist](https://wpackagist.org/search?q=&type=plugin&search=)

Example to install `akismet` plugin:
```bash
docker-compose run --rm composer require --ignore-platform-reqs wpackagist-plugin/akismet
```

#### Install Wordpress Theme

Simple command for install themes:
```bash
docker-compose run --rm composer require --ignore-platform-reqs wpackagist-theme/{THEME_NAME}
```

You can find `themes` on [Wordpress Packagist](https://wpackagist.org/search?q=&type=theme&search=)

Example to install `hueman` theme:
```bash
docker-compose run --rm composer require --ignore-platform-reqs wpackagist-theme/hueman
```

## Documentation

[Bedrock](https://roots.io/bedrock/) documentation is available at [https://roots.io/bedrock/docs](https://roots.io/bedrock/docs).
