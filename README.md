NAME
====

    sherver ---- a bash script web server with CGI handler


SYNOPSIS
========

    sherver [PORT] [ROOT_DIR] [OPTIONS...]
    sherver [status|stop] [MATCHING_ARGS]
    sherver [--help|--version]


DESCRIPTION
===========

  sherver is a web server for files and cgi commands.  It will serve files which are not hidden under the root directory or run scripts in the cgi-bin directory.  It will serve index.html, if it exists, when asked for a directory.  It can be easily modified to use local commands defined within the script.  It logs on standard output.

  CGI scripts should output any extra headers on stdout, followed by a blank line, followed by the main body of output.  Most of the usual CGI variables are set.

  sherver is not built to be particularly secure and intended mainly for use on localhost on private networks.


OPTIONS
=======

    PORT
  Listen on this port number.  Default is 8080.

    ROOT_DIR
  Serve files from this directory.  Default is /nonexistent so if not specified then sherver will only handle local commands.

    --config CONFIG_FILE
  Read the config file.  CGI environment variables may be set, or option variables (with the - replaced with _)

    --virtual-host DEFAULT_NAME
  Run in virtual host mode.  Files are served from ROOT_DIR/VIRTUAL_HOST/ instead of ROOT_DIR/.  VIRTUAL_HOST consists of the host name given by the browser, or DEFAULT_NAME if no Host: field is present.

    --cgi-bin CGI_PATTERN
  Run CGI scripts with execute permission which match this pattern.  Setting this to "" will allow CGI scripts throughout the directory tree.  Default is "cgi-bin/*".

    --index-page INDEX_FILE
  Serve this page when asked for a directory.  Setting this to "dirindex" will turn on directory listings.

    --error-pages ERROR_PREFIX
  Serve files in the form NNN.html prefixed by ERROR_PREFIX when reporting a status code of NNN.

    --keep-alive
  Don't send a Connection: close field to allow the connection to be kept open.  Helps with files with many images in but some browsers may default to keeping the connection open which won't allow any other accesses to the (single threaded) sherver.

    --remote
  Set REMOTE_ADDR and REMOTE_PORT for CGI scripts

    --server | --content-type | --date | --date-modified
  Send the corresponding HTTP response field back to the client.  Content-Type is taken by looking up the file tag in /etc/mime.types.  Date-Modified is taken from the date modified time of the file.

    --version
  Print the version and exit.

    --help
  Print the usage and exit.

    stop
  Stop running shervers.  Any arguments given will do a partial match of the arguments used on running shervers.  It may be useful to specify the PORT first to allow easy matching.

    status
  Give the status of running shervers.  Arguments work as for the stop command.


CONFIG
======

    # directories and patterns
    SERVER_PORT=8080            # PORT
    virtual_host=""             # --virtual-host DeFAULT_NAME
    cgi_bin="cgi-bin/*"         # --cgi-bin CGI_PATTERN
    dot_replace="/nonexistent"
    error_pages=""              # --error-pages ERROR_PREFIX
    index_page="index.html"     # --index-page INDEX_NAME
    root_dir="/nonexistent"     # ROOT_DIR
    temp_dir="/tmp"

    # boolean (non-blank is true)
    content_type=""             # --content-type
    date=""                     # --date
    last_modified=""            # --last-modified
    remote=""                   # --remote
    server=""                   # --server
    keep_alive=""               # --keep-alive
    debug=""                    # --debug

    # commands
    netcat="nc -lnvk " | "nc -lnvp "
    netstat="netstat -A inet --tcp -n"

    # HTTP status messages
    default_protocol="HTTP/1.1"
    ok_msg="200 Ok"
    bad_msg="400 Bad request"
    not_found_msg="404 Not found"
    teapot_msg="418 I am a teapot"
    internal_msg="500 Internal server error"
    unknown_msg="520 Unknown error"

    # pre-set CGI environment variables
    SERVER_SOFTWARE="sherver/0.1 (OPERATING SYSTEM)"
    GATEWAY_INTERFACE="CGI/1.1"
    export SERVER_ADDR=""
    export SERVER_ADMIN=""

    # script set CGI environment variables
    CONTENT_LENGTH
    CONTENT_TYPE
    DOCUMENT_ROOT
    HTTP_ACCEPT
    HTTP_ACCEPT_CHARSET
    HTTP_ACCEPT_ENCODING
    HTTP_ACCEPT_LANGUAGE
    HTTP_COOKIE
    HTTP_HOST
    HTTP_POST_VARS
    HTTP_REFERER
    HTTP_USER_AGENT
    QUERY_STRING
    QUERY_STRING
    REMOTE_ADDR
    REMOTE_PORT
    REQUEST_METHOD
    REQUEST_URI
    SCRIPT_FILENAME
    SERVER_NAME
    SERVER_PROTOCOL


EXAMPLE
=======

  sherver 8081 /srv/root --virtual-host default.com --cgi-bin "*" --index-page dirindex --remote

  Will run a web server on port 8081 with root of /srv/root/example.com for a request of http://example.com:8080/  Any executable files within the tree will be run as a CGI.  Asking for a directory will serve a file listing.  REMOTE_PORT and REMOTE_ADDR will be set for CGI scripts.  Only Content-Length: and Connection: close will be returned as HTTP fields.


NOTES
=====

  Bash is a daft way to write a web server.

  Sherver can only serve one connection at a time.  If running with non-OpenBSD netcat then it can't keep the socket open for multiple connects so may miss some connections - particularly if serving a file with many images/css/js in.  Specifying keep-alive may help - but may also block the port by browsers which decide it's a good idea to keep it open.  Reducing max-concurrent-connections to 1 in a browser may (or may not) help.  If you're trying to serve anything but simple files or CGI scripts - use a proper server.

  dot_replace is what will replace any "/." in a URI.  This prevents serving hidden files or paths which contain "../" in them.  You can set dot_replace to "/." but be warned this will also allow .. in a path.  Setting dot_replace to "/" is not recommended as ".../" will be replaced with "../".

  cgi_bin will check whether a command is executable so getting this wrong may allow serving the source of scripts or executing files.

  error_pages is a prefix so error_pages="error_dir/err" will allow a file such as error_dir/err404.html

  index_page may be an executable script.  Setting index_page="dirindex" invokes the internal command dirindex.

  netcat commands and options will be set according to whether OpenBsd netcat or GNU is present.  Setting netcat="socat TCP4-LISTEN:" may work if socat is installed.

  netstat may be able to use "ss" with the same arguments.


AUTHOR
======
  Written by Tarim.
  Error reports to <sherver-cmd@mediaplaygrounds.co.uk>.


SEE ALSO
========
  nodejs, python
