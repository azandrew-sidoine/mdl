# mdl

MDL library is a php utility binary that internally uses `@drewlabs/code-generator` package to create project source code based on a textual UML definition language written in YAML, or JSON language.

## MDL definition language

The `mdl` definition language is a rapid `OOP Prototyping` tool  that abstract aways   `UML `design graphical tools though a `YAML `or `JSON `configuration file that is used by the client to generate source codes. Below is a basic example of `mdl` modeling language written in YAML.

```yaml
name: 'Drewlabs\\MiaccSdk'
path: "/Users/azandrewsidoine/Workspace/azlabs/lib/php/mdl/src"
directories:
  interfaces: "Contracts"
  mixins: "Traits"

mixins:
  -
    name: "BaseTrait"
    methods:
      -
        name: "baseTraitMethod"

  - name: "MyTrait"
    mixins:
      - "Traits\\BaseTrait"
    methods:
      - name: "sayHello"
        comment: "Say Hello to application users" # optional
        returns: "void" # Optional
        modifier: "public" # Optional
        static: false # Optional
        parameters:
          - name: "name"
            type: string
            default: "PHP"
            optional: true
            variadic: false # Optional
            reference: false # Optional

interfaces:
  - name: "LoggerInterface"
    methods: # Optional
      - name: "log"
        comment: "Log to the console or the standard ouput"
        returns: "void"
        modifier: "public"
        parameters:
          - name: "args"
            default: "PHP"
            optional: false
            variadic: true # Optional
            reference: false # Optional
      -
        name: "baseTraitMethod"
  - 
    name: "MyInterface"
    extends: "Contracts\\LoggerInterface"

classes:
  - name: "BaseClass"
    directory: "Core"
    abstract: true
    implements:
      - "Contracts\\LoggerInterface"
      - 'App\\ValueInterface'
  - name: "AbstractClass"
    abstract: true
    extends: "Core\\BaseClass"
  - name: "MyClass"
    final: true
    extends: "AbstractClass"
    constructor: true
    mixins:
      - 
        "Traits\\DatabaseTrait"
    implements:
      - "Contracts\\TestInterface"
    setter: true
    immutable: true
    properties:
      - name: "console"
        comment: "Injected console object"
        type: 'App\\Console' # Optional
        modifier: "protected" # Optional
        readonly: false  # Optional
        # default: null # Optional
        # constant: false # Optional
    methods:
      - name: "__invoke"
        comment: "Used when the class is invoked as function" # Optional
        returns: "array" # Optional
        modifier: "public" # Optional
        static: false # Optional
        throws:
          - "\\RuntimeException"

```


The design language consist of two main parts:

- The project metadata definition
  As the name implies, the `project metadata` consist of metadata about the project for which the component are being generated. Below is the list of metadata properties that must be defined for a given project:

  - `name` : The name `metadata` defines the project PSR-4 compatible namespace under which the components are generated
  - `path`: This `metadata` defines the base path from which `OOP` component paths are generated.
  - `directories`: This `metadata` is `optional` but help the `mdl-cli`  to separate `interfaces` from core implementations and also provide directory for `mixins` which are PHP `trait` components.
- `OOP` components definition
  `OOP` components consist of actual `classes`, `interfaces/contracts` and `mixin/PHP Trait` objects generated for the given projects. `OOP` components are grouped under:

  - `classes` : which consist of PHP or UML class components
  - `mixins`: which is not part of UML definition language but help in implementing the `DRY` recommandation when developping software application. Basically, they consist of PHP traits components and are generated under the directory specify for `mixins` in the metada configuration.
  - `interfaces`: which contains the list of `interfaces/contracts` that might be implemented in the given project.

## The MDL CLI

Once the definition language is written, we make use of the `mdl-cli` tool to generate the our project source code. The syntax for generating source code from `language defintion` file is:

> <PATH_TO_BINARY_DIR>/mdl <PATH_TO_LANGUAGE_DEFINITION_DIRECTORY>/name.yml

For composer based project and assuming the language definition file is at the root of the project:

> ./vendor/bin/mdl $(pwd)/mdl.yml

### Command options

The `mdl-cli` command support options that allows developpers to change the behaviour of the command line client.

- settters

Setters are configured at class level in the configuration file. But to tell the `cli` tool to generate setters for all classes properties, we use the `--set` or `--setters` command line option.

> ./vendor/bin/mdl $(pwd)/mdl.yml --set

- getters

As with `setters`, `getters` are normally configured at class definition level. But we can also tell the `cli` tool to generate getter class for all classes in the generated code using `--get` or `getters` flag.

> ./vendor/bin/mdl $(pwd)/mdl.yml --get

- strict rule

PHP behave differently whenever the `declare_strict(...)` is added to a script file. By default, `cli` does not add this directive to the generated scripts. To tells the `cli` tool to add the required directive we use `--strict` flag in command line:

> ./vendor/bin/mdl $(pwd)/mdl.yml --get --strict


**Note** The documentation is still under development and might change as API changes occur.
