# google/ruby-runtime

[`google/ruby-runtime`](https://index.docker.io/u/google/ruby-runtime) is a [docker](https://docker.io) base image that makes it easy to dockerize standard [Rack](http://rack.github.io) application.

It can automatically bundle a Rack application and its dependencies with a single line Dockerfile.

It is based on [`google/ruby`](https://index.docker.io/u/google/ruby) base image.

## Usage

- Create a Dockerfile in your Rack application directory with the following content:

        FROM google/ruby-runtime

- Create an executable shell script named `pre-bundler.sh`.
  The script is executed before "`bundle install`".  So you an install prerequisites in it.

- Run the following command in your application directory:

        docker build -t app .

## Sample

See the [sources](/hello) for [`google/ruby-hello`](https://index.docker.io/u/google/ruby-hello) based on this image.

## Notes

The image assumes that your application:

- has a [`Gemfile`](http://bundler.io/gemfile.html) file and its corresponding `Gemfile.lock` for [bundler](http://bundler.io), and the `Gemfile` contains `rack`.
- has a `config.ru` file for Rack.

When building your application docker image, `ONBUILD` triggers:

- Installs gems specified in the `Gemfile` and leverage docker caching appropriately
- Copy the application sources under the `/app` directory in the container

The image uses WEBrick as the application server by default.
You can overwrite it by adding mongrel/thin/puma to Gemfile and configuring in your Dockerfile like

        ENV APPSERVER puma

Or you can just overwrite `ENTRYPOINT` in your Dockerfile like

        ENTRYPOINT ["/usr/local/bin/bundle", "exec", "unicorn", "-c", "unicorn.conf", "config.ru"]
