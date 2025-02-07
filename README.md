# goctopus

Blazing fast graphql fingerprinting toolbox.

[![Go Reference](https://pkg.go.dev/badge/github.com/Escape-Technologies/goctopus.svg)](https://pkg.go.dev/github.com/Escape-Technologies/goctopus)
[![Go Report Card](https://goreportcard.com/badge/github.com/Escape-Technologies/goctopus)](https://goreportcard.com/report/github.com/Escape-Technologies/goctopus)
[![Docker Pulls](https://img.shields.io/docker/pulls/escapetech/goctopus)](https://hub.docker.com/r/escapetech/goctopus)

> ⚠️ Goctopus is still in very early development. Breaking changes are expected.

`````TEXT
goctopus -a rickandmortyapi.com

                    .-'   `'.
                   /         \
                   |         ;
                   |         |           ___.--,
          _.._     |0) ~ (0) |    _.---'`__.-( (_.
   __.--'`_.. '.__.\    '--. \_.-' ,.--'`     `""`
  ( ,.--'`   ',__ /./;   ;, '.__.'`    __
  _`) )  .---.__.' / |   |\   \__..--""  """--.,_
 `---' .'.''-._.-'`_./  /\ '.  \ _.-~~~````~~~-._`-.__.'
       | |  .' _.-' |  |  \  \  '.               `~---`
        \ \/ .'     \  \   '. '-._)
         \/ /        \  \    `=.__`~-.
     jgs / /\         `) )    / / `"".`\
   , _.-'.'\ \        / /    ( (     / /
    `--~`   ) )    .-'.'      '.'.  | (
           (/`    ( (`          ) )  '-;
            `      '-;         (-'
                  _
  __ _  ___   ___| |_ ___  _ __  _   _ ___
 / _` |/ _ \ / __| __/ _ \| '_ \| | | / __|
| (_| | (_) | (__| || (_) | |_) | |_| \__ \
 \__, |\___/ \___|\__\___/| .__/ \__,_|___/ v0.0.16
 |___/                    |_|
[INF] Enumerating subdomains for 'rickandmortyapi.com'
[INF] Found 5 subdomains for 'rickandmortyapi.com' in 15 seconds 276 milliseconds
INFO[0016] Done fingerprinting rickandmortyapi.com
INFO[0016] Found: {"authenticated":false,"domain":"rickandmortyapi.com","schema_status":"OPEN","source":"rickandmortyapi.com","url":"https://rickandmortyapi.com/graphql"}
INFO[0016] Done. Found 1 graphql endpoints
`````

## Usage

Using go:

```BASH
go install -v github.com/Escape-Technologies/goctopus/cmd/goctopus@latest
goctopus -a example.com
```

Using docker:

```BASH
docker run --rm -it escapetech/goctopus:latest -a example.com
```

## Main options & features

It is recommended to use the `-a` flag as a shorthand to enable all the features (if you want detailed results, and don't care about speed).

### Input

Goctopus takes a list of adresses (endpoints and/or urls) as input.
Adresses can be specified directly in the command line or in a file.

#### Command line

The adresses can be specified directly in the command line, comma separated.
Example:

```BASH
goctopus -a example.com,https://example.com/graphql
```

#### Input file

The adresses can be specified in a file, one per line.
The file path should be specified using the `-f` flag.
Example:

```TEXT
example.com
https://example.com/graphql
escape.tech
https://example.com/api
```

```BASH
goctopus -f input.txt
```

### Introspection fingerprinting

The `-introspect` flag enables introspection fingerprinting.
If enabled, goctopus will detect if the introspection of graphql endpoints is enabled.

### Subdomain enumeration

The `-subdomain` flag enables subdomain enumeration.
If enabled, goctopus will try to find graphql endpoints on subdomains of the given domains.
The enumeration is done using [subfinder](https://github.com/projectdiscovery/subfinder).

### Field suggestion fingerprinting

The `-suggest` flag enables field suggestion fingerprinting.
This option needs the introspection fingerprinting (`-introspect`) to be enabled.
When enabled, goctopus will try to detect if the graphql endpoint has field suggestion enabled, if the introspection is closed.
This is useful to bruteforce fields and/or types when introspection is disabled, with tools such as [ClairvoyaceNext](https://github.com/Escape-Technologies/ClairvoyanceNext).

### Output

The `-o` is used to specify the output file path. It defaults to `output.jsonl`.
The output file is in json-lines format.
Each line corresponds to one found graphql endpoint and will contain at least the following fields:

```JSON
{
  "domain": "subdomain.example.com",
  "authenticated": false,
  "url": "https://subdomain.example.com/graphql",
  "source": "example.com"
}
```

The `authenticated` field can be one of the following:

- `true`: The endpoint is a graphql endpoint.
- `false`: The endpoint is a graphql endpoint and requires authentication.

## Additional options

```TEXT
Usage: goctopus [options] [addresses]
[addresses]: A list of addresses to fingerprint, comma separated.
Addresses can be in the form of http://example.com/graphql or example.com. If an input file is specified, this argument is ignored.
[options]:
  -a	(All) Enable all fingerprinting methods: introspection, field suggestion, subdomain enumeration
  -f string
    	Input file
  -introspect
    	Enable introspection fingerprinting
  -o string
    	Output file (json-lines format)
  -s	Silent
  -subdomain
    	Enable subdomain enumeration
  -suggest
    	Enable fields suggestion fingerprinting.
    	Needs "introspection" to be enabled.
  -t int
    	Request timeout (seconds) (default 30)
  -v	Verbose
  -w int
    	Max workers (default 40)
  -webhook string
    	Webhook URL
```

## Docker usage

Using volumes to load the input file and save to the output file:

```BASH
docker run --rm -it -v $(pwd):/data escapetech/goctopus:latest -f /data/input.txt -o /data/output.jsonl
```

Using a specific version:

```BASH
# for version vA.B.C
docker run --rm -it escapetech/goctopus:A.B.C [args]
```

## Roadmap

- [x] Better wordlist for field suggestion fingerprinting, to improve the detection performance and detection rate.
- [ ] Engine fingerprinting.
- [ ] Script analysis.
- [x] Refactor to make goctopus usable as a go package.
- [ ] Document goctopus as a go package.
- [ ] Better flags.
- [x] Better logs.
- [x] Direct cli input.
- [ ] Improve performance further.
- [ ] Resume from output file. (maybe)
- [ ] Custom ascii art. (maybe)
- [x] Docker
