# Codex Protocol | Codex API Documentation _(site.dev-codexprotocol-com)_

[![standard-readme compliant](https://img.shields.io/badge/readme%20style-standard-brightgreen.svg?style=flat-square)](https://github.com/RichardLitt/standard-readme)

> Codex API documentation built with [Slate](https://github.com/lord/slate)

This repository contains source code for The Codex API documentation hosted on [https://dev.codexprotocol.com](https://dev.codexprotocol.com).

## Table of Contents

- [Install](#install)
- [Usage](#usage)
- [Maintainers](#maintainers)
- [Contribute](#contribute)
- [License](#license)


## Install

This documentation is built with [Slate](https://github.com/lord/slate), which
requires some prerequisites for development:

  - **Linux or macOS** — Windows may work, but is unsupported.
  - **Ruby, version 2.3.1 or newer**
  - **Bundler** — If Ruby is already installed, but the `bundle` command doesn't
    work, just run `gem install bundler` in a terminal.


## Usage

After prerequisites have been installed, you can initialize and start Slate. You
can either do this locally, or with Vagrant:

```bash
# either run this to run locally
bundle install
bundle exec middleman server

# OR run this to run with vagrant
vagrant up
```

After you make markdown changes, you can deploy change by running `./deploy`.
For more information see [Slate's deployment documentation](https://github.com/lord/slate/wiki/Deploying-Slate).


## Maintainers

- [Colin Wood](mailto:colin@codexprotocol.com) ([@Anaphase](https://github.com/Anaphase))


## Contribute

If you have any questions, feel free to reach out to one of the repository [maintainers](#maintainers)
or simply [open an issue](https://github.com/codex-protocol/service.codex-registry-api/issues/new)
here on GitHub. If you have questions about Codex Protocol in general, feel free
to [reach out to us Telegram!](https://t.me/codexprotocol)

[Pull Requests](https://github.com/codex-protocol/service.codex-registry-api/pulls)
are not only allowed, but highly encouraged! All Pull Requests must pass CI
checks (if applicable) and be approved by at least one repository
[maintainer](#maintainers) before being considered for merge.

Codex Labs, Inc follows the [Contributor Covenant Code of Conduct](https://contributor-covenant.org/version/1/4/code-of-conduct).


## License

[Apache License 2.0](LICENSE) © Codex Labs, Inc
