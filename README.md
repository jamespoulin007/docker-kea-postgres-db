PostgreSQL 9.5 container as back-end support for Kea DHCP server. Use repo https://github.com/jamespoulin007/docker-kea-dhcp for the KEA DHCP server.

# What is it?

This container is created from postgres:9.5 and will create the schema used by Kea (v1.1.0), exposing its port 5432/tcp

# How to use it?

A practical example is the following:

``` bash
docker run --name postgres \
       -p 5432:5432 \
       -e POSTGRES_PASSWORD=dummy_password \
       -e POSTGRES_USER=dummy_user \
       -e POSTGRES_DB=dummy_db \
       -v <volume-to-hold-postgres-data>:/var/lib/postgresql/data \
       -d ungleich/ungleich-postgres
```


# What does it do?
This will create the container with its name as "postgres", map the TCP 5432 host's port to the container equivalent. Also, it will create the superuser as "dummy_user" with password "dummy_password" and the default database as "dummy_db". Once created, the Kea schema is taken from *dhcpdb_create.pgsql* and applied in the default database. These credentials can be used by Kea to connect to Postgres.

# How can my Kea container connect to it?
There are several ways you can link containers together. One of them is creating a custom network:

```bash
docker network create --driver=bridge <your-custom-network>
```

Then, create your postgres container, specifying what network to connect (--network <your-custom-network>) and, optionally, what network alias to use (--network-alias <you-alias>). You can remove port mapping, as both containers will reside on the same network.

Finally, make sure your Kea container is connected to your new custom network. If not, you can do that like this:

```bash
docker network connect <your-custom-network> <you-kea-container>
```

This way, you can communicate with your Postgres container from your Kea container, both using its name (in this example, "postgres") or using its network alias.

Obviously, you wil have to update your kea.conf file in order to connect to Postgres. For further information regarding Kea administration, please refer to [Kea's official documentation](http://kea.isc.org/docs/kea-guide.html)

For further information regarding Docker networking, please refer to the [official documentation](https://docs.docker.com/).
