# dotnet-repo-template

This is a template repository for repositories with .NET code.

## Contributors

[Tom Deseyn](https://github.com/tmds/)

## Using this template

Checkout your repository and add the files from this repository.

```sh
git clone --depth 1 https://github.com/tmds/dotnet-repo-template && rm -rf dotnet-repo-template/.git && cp -r dotnet-repo-template/. . && rm -rf dotnet-repo-template
```

You can include these files you've added for your next commit by running:
```sh
git add -A
```
## CI

This section provides instructions on how you can enable CI for your repository.

### Travis

These are instructions when using Travis CI: https://www.travis-ci.com/.

The following configuration shows how to:
* Upload nuget packages to a NuGet feed for tags, and commits on `main` branch.
* Create draft releases on GitHub when pushing a tag

1. First, you need to enable your repository in Travis.

2. To upload the NuGet packages, we need to include set the `NuGetApiKey` environment variable.
The NuGet server is set in `Directory.Build.props`.

To create the draft release on GitHub, we need to include a token.

These secrets are encrypted using the travis gem:

```
# Install the travis gem
gem install travis

# If you are using travis-ci.com instead of travis-ci.org, you need to login first.
# travis login --pro

# NuGetApiKey
travis encrypt NuGetApiKey="deadbeef-3e0e-11eb-81d8-7000c23bcef2"

# Personal access token
travis encrypt deadbeef7000c23bcef2
```

3. Add a `.travis.yml` file to your repository and replace the secrets.

```yml
language: csharp
mono: none
dotnet: 5.0 # .NET version

env:
  global:
    # NuGetApiKey
    secure: vC9QTxXicRgN3ij5n5qjq7RntLjQ9dy3JjwYBi/rtcublwnaA2OryLYG+s4sXGSlyk9tBdsWg8kt+VEqiF1cDQy76iYpTSd06CZ/EJqrqrwYTiGHi7kAbZCrRVjwDnYsjHNlzEY9P8Te1YPtdaIabJN7TAQT+amcH047nw9eNZEQWhrsso7quE+C2GAJaz3ANtdpcdHGIdfrEJ636hRRVd3H9ZYnVUO8eqXds6v6t/jhCvLBRbB/bFSk0RRC50JwUJpBdM14pH16pYfSZwKV+rtodUnSzga5LY+dFUoQj2ymDMkNiaOAopNy0OB+E45BXNGPBahBihkRlgs87KazVkWbdsM6ctBqBF97tiwA8joo/0mSJuzBwYEQLLxXx5n8BHzUpBhU+h3R2BWm1WsOD786/j4Cj92w30bpj6RpDyVRaNPL69hJRSD+7+2j/N/FoJHrUFNe20b+BYr5dwCFMHPO4INGjqQsP7zUmPtpAAMNeINJ4zh3Ee62j2siI6Vz7ZJL24su4VrHXb5hZWqwMF0RNgDvOzB0P5ZuI9usv6Le/EydJkhr+zH9kBlSPsxujWcpt6+OaUPm8DIO89hzA48As+jJf4tBmrgwuNLb7av/ZuxI9Hp7ifQzArWB3yFkIW25mwthX6hysV1k9ch9yirmHDtkdUj/qE2yl9DUcwk=

script:
  - dotnet test
  - dotnet build

deploy:
  - provider: script
    edge: true
    script: dotnet msbuild -t:PushPackages
    on:
      all_branches: true
      condition: $TRAVIS_BRANCH == "main" || -n $TRAVIS_TAG
  - provider: releases
    edge: true
    file: artifacts/packages/Release/*.nupkg
    token:
      # Github 'Personal access token' with 'pubic_repo' scope.
      secure: "dDIuXQkQKlLl9L94uJUqydG71TwP27f5LOsx5ZqDuzea83Nl2bLNkVWV5+F0GAxFm4aD+g/mNdFtdHNZHN4Eg9a6+KVO5S7X6beI4hHAAnfZ2+Zeq1KB9hYohaQzjGy4Biqs4nw3tHjf8FMgUmBDp/ah9Ve23gcjfx+qBs8xc3T3yNJNipR/qmOXDJOARxh4NqVHoXrP6kyaKu+Sac/TjdId4nc2isKUAUqAhd1rM7dW4hMAmO2HQ2gi0KGD9890iWNHEYOLuo616R3+eIvFjr51Dc2dSjwWJ/azL6tzrFvuZGuXK3IJLvxw+wReelfh9vm3r665+kRhmH+Qccf4BFSA/pURMJCUzcEw+rEzqlZsNku6JBKCv6Sw9ZFycUznEYDroLd489j3WX/aQiaaFxdzLVmU1hpRuEIT33PgVmN7GUybOIpzl2OP+lOzdJw8LGx8vuf1+LTNuGZ0KpKeCoyUlaMVYQuxMTp+mEUpyJo7qO5vmdBFarnHezMQV2UVNqyFCGv3lWcd1C0stpPIwxPqaTH0P9+ziTL4yt7vSnD3wjGcqneN5RB99hrZMQCXmdP0jN/I9EjeWrF2459JLbtz9w7bm9lsby9MOjuanQ06Lxu0qtLFnVR44j5Ca7PZj/Su8/zQWdxULmIarLe4dpX5mhCnASb8mno4P30EMUM="
    draft: true
    on:
      tags: true
```