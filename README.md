# ADO.NET Specification Tests

This library provides a suit of [xUnit.net](https://xunit.github.io/)-based tests for ADO.NET providers, i.e.,
classes that inherit from `DbConnection`, `DbCommand`, `DbDataReader`, etc.

It intends to clarify ambiguous areas in the ADO.NET documenation and help ensure that various providers act
consistently (when the behaviour doesn't depend on the specific target database server).

## How to Use

Add a reference to the [NuGet package](https://www.nuget.org/packages/AdoNet.Specification.Tests) to your test project.

Create a class that implements `IDbFactoryFixture`. In most cases, the easiest way to do this
is to inherit from ` DbFactoryFixtureBase<TDatabase>` where `TDatabase` is one of the built-in
classes from the `AdoNet.Specification.Tests.Databases` namespace. Then implement the
`Factory` property, which returns a `DbProviderFactory` for your ADO.NET implementation,
and the `ConnectionString` property, which must return a valid connection string that the test
suite can use to connect to a database. See
[this example](https://github.com/mysql-net/AdoNetApiTest/blob/master/tests/SqlClient.Tests/SqlClientDbFactoryFixture.cs) for
`System.Data.SqlClient`.

Create one public derived class from each `XTestBase<T>` test base class. Add a public
constructor that takes an instance of your `IDbFactoryFixture` implementation and passes it
to the base class.

As necessary, override failing test methods to customize/skip them for your provider.

## How to Build a Comparison Chart

Clone this repo.

Run the required database servers locally:

```
docker run --name mssql -e ACCEPT_EULA=Y -e MSSQL_SA_PASSWORD=Pa$$word -p 1433:1433 -d microsoft/mssql-server-linux:2017-latest
docker exec mssql /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Pa$$word -Q "create database sqlclient;"

docker run --name mysql -e MYSQL_ROOT_PASSWORD=test -p 3306:3306 -d mysql:5.7
docker exec mysql mysql -uroot -ptest -e "create schema dotconnect collate utf8mb4_bin;"
docker exec mysql mysql -uroot -ptest -e "create schema mysqlconnector collate utf8mb4_bin;"

docker run --name postgres -e POSTGRES_USER=root -e POSTGRES_PASSWORD=test -p 5432:5432 -d postgres
```

Run the `AdoNetApiTest` program:

1. In Visual Studio 2017, set it as the Start Up Project and press `Ctrl+F5` to launch, or
2. From a command prompt: `dotnet run -p src\AdoNetApiTest\AdoNetApiTest.csproj`

Note that some DB providers require the full .NET Framework 4.7; this program has only been tested on Windows (not with mono).

The program will run all providers' test suites, then open the results in your default browser.

## License

[MIT](LICENSE)