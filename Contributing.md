## Writing integration Tests

It is really difficult and sometimes impossible to tell if the code generated by the tool
is functional and type sound. Writing unit tests for this is not enough and one has
actually use the guardrail as a CLI tool to generate complete clients/service to make sure everything works as expected.

That why there is a dedicated submodule `modules/sample` for testing of by actually generating fully functional client and server codebases
by provided swagger specs with the ability to run test code against them.

### Adding a new swagger spec

The way it work is you have to first to add a new line inside of `fullRunTask` task in `build.sbt`

```scala
lazy val runExample: TaskKey[Unit] = taskKey[Unit]("Run with example args")

fullRunTask(
  runExample,
  Test,
  "com.twilio.guardrail.CLI",
  """
  --defaults --import akka.NotUsed --import support.PositiveLong
  --client --specPath modules/sample/src/main/resources/petstore.json --outputPath modules/sample/src/main/scala --packageName clients.http4s --framework http4s

  --client --specPath modules/sample/src/main/resources/edgecases/defaults.yaml --outputPath modules/sample/src/main/scala --packageName edgecases.defaults
  --client --specPath modules/sample/src/main/resources/custom-header-type.yaml --outputPath modules/sample/src/main/scala --packageName tests.customTypes.customHeader
""".replaceAllLiterally("\n", " ").split(' ').filter(_.nonEmpty): _*
)
```

- `--specPath` has to point to the newly added .yaml file
- `--outputPath` should be pointing to `modules/sample/src/main/scala`
- `--packageName` package name to use

Also you can reuse an already existing .yaml file

### Adding tests

Define your tests in `./modules/sample/src/test/scala` make sure to use
imports corresponding the previously defined `packageName`

### Running the tests

Use `example` command inside of SBT session to run code generation and execute the tests