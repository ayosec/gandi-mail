# gandi-mail

This repository contains a script to manage email addresses and aliases in a
[Gandi]. It only contains a small set of the operations supported by [Gandi],
but those are the only ones that I use frequently.

If you need a more complete support please check out the [official CLI][cli]. It
is [deprecated][cli-deprecated], but it should be still working.

## Installation

You can put the script in any directory of your `$PATH`.

```console
$ sudo install -v -o root -g root -t /usr/local/bin/ gandi-mail
```

It is also possible to use it directly from the repository directory.

## Configuration

Before using the script you have to create an API key for your [Gandi] account:

1. Open <https://account.gandi.net>.
2. Open the **Security** section.
3. On **Production API key**, generate a new one and store it in an `api-key` file.

See <https://api.gandi.net/docs/authentication/> for more details.

The `api-key` can be encrypted with GnuPG. In such case, the name has to be
`api-key.gpg`.

The file (either `api-key` or `api-key.gpg`) has to be stored in the
`$XDG_CONFIG_HOME/gandi-mail` directory (usually, `$HOME/.config/gandi-mail`). 

## Usage

If you run `gandi-mail` with no arguments it prints a summary of the options:

```console
$ gandi-mail
Usage /usr/local/bin/gandi-mail [command]

  COMMANDS:

  domains                 Show domains owned by your user.
  domain DOMAIN           Show information about a domain.

  emails ls DOMAIN        Show emails in a specific domain.
  emails add EMAIL        Add a new email to a domain.

  alias ls EMAIL          Show aliases for an email address.
  alias add EMAIL ALIAS   Add a new alias to the email address.
  alias rm EMAIL ALIAS    Remove an alias.
```

### List Domains

`gandi-mail domains` shows domains visible to the account.

```console
$ gandi-mail domains
FQDN         AUTORENEW  CREATED AT  REGISTRY ENDS AT
aaaaaa.com   true       2011-11-11  2023-01-02
bbbbbbb.dev  true       2020-01-02  2023-03-04
```

### Domain Information

`gandi-mail domain $DOMAIN` shows a JSON object with all the information
provided by [Gandi] in its API:

```console
$ gandi-mail domain aaaaaa.com
{
  "fqdn": "aaaaaa.com",
  "tld": "com",
  "status": [
    "clientTransferProhibited"
  ],
  [...]
```

### List Mailboxes

`gandi-mail emails ls DOMAIN` shows a list of all mailboxes created in the
specified domain.

```console
$ gandi-mail emails ls recense.dev
ADDRESS               TYPE      QUOTA USED  ALIASES  ID
xxxxxx@bbbbbbb.dev    standard  4           2        00000000-0000-0000-0000-000000000000
yyyyyyyy@bbbbbbb.dev  standard  5           5        00000000-0000-0000-0000-000000000001
````

### Adding a New Mailbox

`gandi-mail emails add EMAIL` creates a new mailbox in the email found in the
email address.

For example, the command `gandi-mail emails add foo@example.com` creates a
mailbox with the login `foo` in the domain `example.com`.

### List Aliases for a Mailbox

`gandi-mail alias ls EMAIL` shows the existing aliases for the given mailbox.

```console
$ gandi-mail alias ls xxxxxx@bbbbbbb.dev
foo
bar
```

### Creating New Aliases

`gandi-mail alias add EMAIL ALIAS` adds a new alias to a mailbox.

For example, to create the address `bar@aaaaaa.com` as an alias of
`foo@aaaaaa.com`, we can use the following command:

```console
$ gandi-mail alias add foo@aaaaaa.com bar
{
  "message": "The email mailbox is being updated."
}
```

### Removing an Alias

`gandi-mail alias rm EMAIL ALIAS` removes an alias from a mailbox.



[Gandi]: https://gandi.net/
[cli]: https://cli.gandi.net/
[cli-deprecated]: https://github.com/Gandi/gandi.cli/commit/cba249049de65b72ac098d30db0d273b414677fc
