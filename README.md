# puppet-cpan

[![Puppet Forge Version](http://img.shields.io/puppetforge/v/ccin2p3/cpan.svg)](https://forge.puppetlabs.com/ccin2p3/cpan)
[![Puppet Forge Downloads](http://img.shields.io/puppetforge/dt/ccin2p3/cpan.svg)](https://forge.puppetlabs.com/ccin2p3/cpan)
[![Travis branch](https://img.shields.io/travis/ccin2p3/puppet-cpan/master.svg)](https://travis-ci.org/ccin2p3/puppet-cpan)
[![By Meltwater](https://img.shields.io/badge/by-meltwater-28bbbb.svg)](http://underthehood.meltwater.com/)
[![Maintenance](https://img.shields.io/maintenance/yes/2016.svg)](https://github.com/ccin2p3/puppet-cpan/commits/master)
[![license](https://img.shields.io/github/license/ccin2p3/puppet-cpan.svg)](https://github.com/ccin2p3/puppet-cpan/blob/master/LICENSE)

#### Table of Contents

1. [Overview](#overview)
2. [Module Description - What the module does and why it is useful](#module-description)
3. [Setup - The basics of getting started with cpan](#setup)
    * [What cpan affects](#what-cpan-affects)
    * [Beginning with cpan](#beginning-with-cpan)
4. [Usage - Configuration options and additional functionality](#usage)
5. [Reference - An under-the-hood peek at what the module is doing and how](#reference)

## Overview

Handle installations of [cpan modules](http://www.cpan.org/modules/) via puppet.

## Module Description
The cpan module sets up capn on a server

## Setup

### What puppet-cpan affects

* cpan package.
* cpan configuration file.

## Usage

All options and configuration can be done through interacting with the parameters
on the cpan class and the cpan resource type.  These are documented below.

## cpan class

```puppet
class {'::cpan':
  manage_config  => true,
  manage_package => true,
  package_ensure => 'present',
  installdirs    => 'site',
  local_lib      => false,
  config_hash    => { 'build_requires_install_policy' => 'no' },
  ftp_proxy      => 'http://your_ftp_proxy.com',
  http_proxy     => 'http://your_http_proxy.com',
  environment    => {
  	'OPTIONAL_ENVIRONMENT_VAR_1' => '1',
  	'OPTIONAL_ENVIRONMENT_VAR_2' => '/foo/bar',
  },
}
```

### Beginning with cpan

```puppet
include '::cpan'
cpan { "Clone::Closure":
  ensure  => present,
  require => Class['::cpan'],
  force   => true,
}
```

### Package Management

To avoid conflicts with in house package management, use:

```puppet
class {'::cpan':
  manage_package => false,
}
```

### Install Destination

To control target installation path, use:

```puppet
class {'::cpan':
  installdirs => 'vendor',
}
```

Any of `site` (default), `perl` and `vendor` are accepted.

To further control the location of installed modules, you can use [local::lib](http://search.cpan.org/perldoc?local::lib):

```puppet
cpan { 'Foo::Bar':
  ensure    => present,
  local_lib => '/opt',
}
```

This will install the module into `/opt`. Of course you need to adjust `@INC` of your code in order to use that
new location, *e.g.* by using `perl -Mlocal::lib=/opt myapp.pl`.

You can also define the default value of `local_lib` for all `cpan` resources:

```puppet
Cpan { local_lib => '/opt' }
```

### Configure a Proxy

```puppet
class {'::cpan':
  ftp_proxy  => 'http://your_ftp_proxy.com',
  http_proxy => 'http://your_http_proxy.com',
}
```

### Add additional environment variables

Some modules may require additional environment variables to be set *e.g.* during install, like `DBD::Oracle` or `DBD::DB2`.

```puppet
cpan {'DBD::DB2':
  ensure      => 'latest',
  environment => {
   'DB2_HOME' => '/path/to/DB2,
  }
}
```

### Overriding exists code

Some modules like `Sys::RunAlone` don't return a code 0 when invoked with `perl -M$MODULE_NAME -e1`.
This means there's no way for puppet to check if they're installed or not (in an agnostic way).
Therefore you'll have to change the `exists_strategy`:

```puppet
cpan {'Sys::RunAlone':
  ensure => 'present',
  exists_strategy => 'find',
}
```

Currently two strategies are implemented:

#### include (default)

Tries to include module:

```
perl -M$MODULE_NAME
```

#### find

Tries to find module in `@INC` list much like `pmpath` is doing.

## Reference

## Classes

* cpan: Main class for installation and service management.
* cpan::install: Handles package installation.
* cpan::params: Different configuration data for different systems.
* cpan::config: Handles the cpan service.

### Parameters

#### `manage_config`

#### `manage_package`

#### `installdirs`

#### `local_lib`

#### `config_template`

#### `config_hash`

#### `package_ensure`

#### `ftp_proxy`

#### `http_proxy`

#### `environment`

## Limitations

This module has been built on and tested against Puppet 3.x and Puppet 4.x

The module has been tested on:

* RedHat Enterprise Linux 6/7
* Debian 6/7
* CentOS 6/7

Testing on other platforms has been light and cannot be guaranteed.

## Maintainer

* 2019-now [CC-IN2P3](https://github.com/ccin2p3)
* 2012-2017 [Meltwater](https://github.com/meltwater)
