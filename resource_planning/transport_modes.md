# Transport Modes

Job efficiency and time required for its completion are highly dependent
on the data transport mode. Transport mode is a method used by the Veeam
proxy to retrieve VM data from the source host and write VM data to the
target destination.

The primary role of the backup proxy is to provide an optimal path for
backup traffic and to enable efficient data transfer. Therefore, to
deploy a backup proxy, you need to analyze the connection between the
backup proxy server and storage it will be accessing. Depending on the
type of connection, the backup proxy can be configured in one of the
following modes (preferred mode is Direct SAN, if possible; all modes
will be explained later in more details):