# Prompt — atualizar classes Dart 3.13 para primary constructors

Use este arquivo como prompt. Fontes oficiais:

- [Primary constructors](https://dart.dev/language/primary-constructors)
- [Class modifiers](https://dart.dev/language/class-modifiers)
- [Class modifiers reference](https://dart.dev/language/modifier-reference)

---

## Origem e destino

- **Código antigo (`HOJE`):** Flutter **3.38.10**, Dart **3.10**. Primary constructor **não existe** nessa versão.
- **Destino:** Flutter **3.47.7**, Dart **3.13** (ver [`flutter-3.38.10-to-3.47.7-verificacao.md`](flutter-3.38.10-to-3.47.7-verificacao.md)).
- Primary constructor exige **language version 3.13+**. Antes de converter, confira `environment: sdk:` no `pubspec.yaml` (ou um `// @dart=` no arquivo). Se for **menor que 3.13**, a conversão **quebra a compilação**: não converta, **sugira** a mudança do `sdk` e **pergunte antes** (ver [Se a conversão quebrar o código](#se-a-conversão-quebrar-o-código)).
- Código vindo do 3.10 pode ter `final` / `var` em parâmetros de funções, métodos, closures ou construtores no body. No 3.13 isso vira erro `extraneous_modifier`. Não é tarefa desta migração: **não mexa**, só **avise** onde aparece e sugira `dart fix`.

---

## Padrão obrigatório

**Primary constructor é o padrão que deve ser adotado.** Dart 3.13+. Toda classe nova, toda classe reescrita e toda atualização de código existente usam essa sintaxe. A forma antiga (campos no body + `Nome(this.x)`) não é aceita como resultado.

O lado `// PRIMARY CONSTRUCTOR` dos exemplos abaixo é o formato canônico. O lado `// HOJE` existe só para mostrar de onde veio a mudança — não reproduza `HOJE`.

## Tarefa

Reescreva classes Dart/Flutter do projeto para **primary constructors**. Preserve comportamento, modifiers, herança, `const`, factories e nomes. Não invente API. Não remova métodos. Só enxugue declaração de campos + construtor no header, no padrão dos exemplos.

**Não refatore o código.** Mude **só** as linhas que passam a usar primary constructor:

- O header da classe (`class Nome ...`) vira o header com params.
- Os campos que foram para o header saem do body.
- O construtor antigo (`Nome(this.x)` / `const Nome({...})`) sai do body.

Todo o resto fica **exatamente igual**: métodos, `build`, getters, imports, comentários, ordem, formatação, nomes e lógica. Não renomeie, não reordene, não reformate, não extraia widgets, não "melhore" nada.

**Anotações: mantenha só as que já existiam.** As que já estavam no código (ex.: `@override`) ficam exatamente onde estão. Não adicione nenhuma nova (`@staticIconProvider`, `@metadata`, `@immutable`, …) e não remova as existentes.

**Sempre pergunte se houver dúvida.** Não adivinhe: se algo não estiver claro, pergunte antes de mudar o código.

### Fluxo de aprovação — um componente por vez

Cada componente (classe / widget) alterado **precisa ser aprovado**. Nunca altere vários de uma vez.

1. Escolha **um** componente.
2. **Mostre a proposta** antes de aplicar:
   - **Componente:** nome e arquivo.
   - **Tipo:** qual tipo/exemplo deste prompt foi usado.
   - **Atual** e **proposto**, lado a lado (só as linhas que mudam).
   - **Observações:** o que ficou no formato antigo e por quê, avisos (`extraneous_modifier`, `sdk`, etc.).
3. **Pergunte:** "Aprova a alteração de `<Componente>`?"
4. **Só aplique depois de aprovado.** Se não aprovar, não aplique: ajuste conforme o pedido e mostre de novo.
5. Depois de aplicado, **pergunte:** "Deseja seguir para o próximo componente (`<Próximo>`)?"
6. Só passe para o próximo com o "sim". Se a resposta for não, pare.

**Siga os exemplos abaixo para se espelhar nas mudanças.** Cada bloco tem `// HOJE` (código antigo, não usar) e `// PRIMARY CONSTRUCTOR` (padrão a adotar). Ache o exemplo cujo modifier e forma de construtor batem com a classe alvo e copie esse padrão — não invente outra grafia.

Como espelhar:

1. Identifique o modifier da classe (`class`, `abstract`, `base`, `interface`, `final`, `sealed`, `mixin class`, …) e o tipo de construtor (`const`, nomeado, `super.`, named params, body/`assert`, factory).
2. Abra o exemplo correspondente neste prompt.
3. Aplique a **mesma transformação** `HOJE → PRIMARY CONSTRUCTOR` na classe do projeto, só trocando nomes, tipos e campos. O resultado tem que ficar igual ao lado `PRIMARY CONSTRUCTOR`.
4. Se não houver exemplo idêntico, use o mais próximo (mesmo modifier + mesmo tipo de construtor) e as regras. Não misture padrões. Não volte à sintaxe antiga.
5. Para recursos que não dependem do modifier (enum, extension type, escopos, private named, restrições), use a parte **Tipos de primary constructor — referência para a alteração** no fim deste arquivo, com o Pode / Não pode de cada tipo.

## Regras

0. **Primary constructor é o padrão.** Código gerado, migrado ou editado declara campos e construtor no header. Não deixe `class Nome { final T x; Nome(this.x); }` quando der para escrever `class Nome(final T x);`.
1. Preserve o class modifier **exato** (`abstract`, `base`, `interface`, `final`, `sealed`, `mixin class`, …).
2. Mova campos `this.x` / `required this.x` para declaring params no header:
   - `this.x` mutável → `var Tipo x`
   - `this.x` final → `final Tipo x`
   - `super.x` → `super.x` (igual)
   - named `required this` → `required final Tipo x` / `required var Tipo x`
3. `const Nome(...)` vira `class const Nome(...)`. O `const` vai **depois** de `class`, **antes** do nome.
   - Com modifier: `final class const Nome(...)` / `abstract class const Nome(...)`
   - Nunca: `const abstract class` / `const final class`
4. Construtor nomeado no header: `class Nome.nome(...)` / `sealed class Result._()`.
5. Body do construtor: `this : assert(...) { ... }` dentro da classe.
6. Factory fica no **body**, **exatamente como está hoje** (`factory Nome.nome(...)`). Não vai para o header. Não use sintaxe concisa de construtor (`new(...)`, `new nome(...)`, `factory nome(...)`).
7. Classe **sem construtor** (ex.: `class Band with Musical {}`): **não mexe**, fica como está. Não adicione `()` no header — a documentação não mostra primary constructor com lista vazia.
8. Não invente `abstract sealed`. Não coloque params em `mixin class`.
9. Subtipo de `base` / `final` / `sealed` continua `base` / `final` / `sealed`.
10. `extends` / `with` / `implements` ficam **depois** da lista de params.
11. **Não refatorar.** Só as linhas de header, campos e construtor mudam. O resto do arquivo fica idêntico.
12. **Anotações: só as que já existiam.** Manter as do código original; não adicionar novas (`@staticIconProvider`, `@metadata`, …).
13. **Na dúvida, pergunte.** Se não estiver claro qual exemplo seguir, se a classe não bate com nenhum padrão, ou se a mudança exigiria mexer em algo além do header/campos/construtor, **pare e pergunte antes de alterar**. Não chute.
14. **Não quebre o código.** Se a classe tem `late`, `async` / `async*` / `sync*` / `=>` ou outra coisa que o primary constructor ainda não aceita, **mantenha o bloco com a implementação antiga**. Se algum ajuste for **extremamente necessário**, **não aplique**: sugira a alteração e pergunte antes. Ver [Quando manter a implementação antiga](#quando-manter-a-implementação-antiga).
15. **Um componente por vez, com aprovação.** Mostre a proposta, espere aprovar, aplique, e pergunte se deseja seguir para o próximo. Ver [Fluxo de aprovação](#fluxo-de-aprovação--um-componente-por-vez).

Ordem do header:

```text
[abstract] [base | interface | final | sealed] [mixin] class [const] Nome[.ctor](params)
  [extends Super] [with Mixin] [implements Iface]
```

---

## Exemplos para se espelhar

Use só estes pares. `HOJE` = legado (origem da mudança). `PRIMARY CONSTRUCTOR` = **padrão que deve ser adotado** (formato de saída).

## Sintaxe básica

### Campos mutáveis

```dart
// HOJE
class Ponto {
  int x;
  int y;
  Ponto(this.x, this.y);
}

// PRIMARY CONSTRUCTOR
class Ponto(var int x, var int y);
```

### Campo final

```dart
// HOJE
class Usuario {
  final String nome;
  Usuario(this.nome);
}

// PRIMARY CONSTRUCTOR
class Usuario(final String nome);
```

### Const

```dart
// HOJE
class Cor {
  final int r;
  final int g;
  final int b;
  const Cor(this.r, this.g, this.b);
}

// PRIMARY CONSTRUCTOR
class const Cor(final int r, final int g, final int b);
```

### Construtor nomeado

```dart
// HOJE
class Convidado {
  final String apelido;
  Convidado.anonimo(this.apelido);
}

// PRIMARY CONSTRUCTOR
class Convidado.anonimo(final String apelido);
```

### Body + initializer (`this :`)

```dart
// HOJE
class Pedido {
  final String id;
  int quantidade;
  Pedido(this.id, this.quantidade)
    : assert(quantidade > 0, 'Quantidade deve ser positiva') {
    debugPrint('Pedido $id criado com $quantidade item(ns)');
  }
}

// PRIMARY CONSTRUCTOR
class Pedido(final String id, var int quantidade) {
  this : assert(quantidade > 0, 'Quantidade deve ser positiva') {
    debugPrint('Pedido $id criado com $quantidade item(ns)');
  }
}
```

### Campo `var` + método que muta

```dart
// HOJE
class Cachorro {
  String nome;
  Cachorro(this.nome);

  void mudarNome(String nome) {
    this.nome = nome;
  }
}

// PRIMARY CONSTRUCTOR
class Cachorro(var String nome) {
  void mudarNome(String nome) {
    this.nome = nome;
  }
}
```

### Widget do dia a dia (campos final citados duas vezes)

```dart
// HOJE
class ProductCard extends StatelessWidget {
  const ProductCard({
    super.key,
    required this.title, // 1ª menção de title
    required this.price, // 1ª menção de price
    this.onTap, // 1ª menção de onTap
  });

  final String title; // 2ª menção de title
  final double price; // 2ª menção de price
  final VoidCallback? onTap; // 2ª menção de onTap

  @override
  Widget build(BuildContext context) {
    return Card(
      child: ListTile(
        leading: const Icon(Icons.shopping_bag),
        title: Text(title),
        subtitle: Text('R\$ ${price.toStringAsFixed(2)}'),
        onTap: onTap,
      ),
    );
  }
}

// PRIMARY CONSTRUCTOR
class const ProductCard({
  super.key,
  required final String title,
  required final double price,
  final VoidCallback? onTap,
}) extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Card(
      child: ListTile(
        leading: const Icon(Icons.shopping_bag),
        title: Text(title),
        subtitle: Text('R\$ ${price.toStringAsFixed(2)}'),
        onTap: onTap,
      ),
    );
  }
}
```

---

## Class modifiers

Tabela para **biblioteca externa** ([referência](https://dart.dev/language/modifier-reference)):

| Declaração | Construir | Estender | Implementar | Mixin | Exhaustive |
|---|---|---|---|---|---|
| `class` | sim | sim | sim | não | não |
| `base class` | sim | sim | não | não | não |
| `interface class` | sim | não | sim | não | não |
| `final class` | sim | não | não | não | não |
| `sealed class` | não | não | não | não | sim |
| `abstract class` | não | sim | sim | não | não |
| `abstract base class` | não | sim | não | não | não |
| `abstract interface class` | não | não | sim | não | não |
| `abstract final class` | não | não | não | não | não |
| `mixin class` | sim | sim | sim | sim | não |
| `base mixin class` | sim | sim | não | sim | não |
| `abstract mixin class` | não | sim | sim | sim | não |
| `abstract base mixin class` | não | sim | não | sim | não |
| `mixin` | não | não | sim | sim | não |
| `base mixin` | não | não | não | sim | não |

Inválido:

- `abstract` + `sealed` (`sealed` já é implicitamente abstract)
- `sealed` + `base` / `interface` / `final`
- `base` + `interface` + `final` (mutuamente exclusivos)
- `mixin` + `interface` / `final` / `sealed`
- `mixin` (não `mixin class`) **não** tem primary constructor com parâmetros
- `mixin class` só aceita primary constructor **trivial**: sem params, sem initializer, sem body

---

### 1) Sem modifier — `class`

Qualquer biblioteca: construir, estender, implementar.

```dart
// HOJE
class Vehicle {
  final String name;
  Vehicle(this.name);

  void moveForward(int meters) {
    // ...
  }
}

class Car extends Vehicle {
  final int passengers;
  Car(super.name, this.passengers);
}

// PRIMARY CONSTRUCTOR
class Vehicle(final String name) {
  void moveForward(int meters) {
    // ...
  }
}

class Car(super.name, final int passengers) extends Vehicle;
```

---

### 2) `abstract`

Não instancia. Pode estender e implementar. Métodos podem ser abstratos. Factory, se existir, fica no **body**, não no header.

```dart
// HOJE
abstract class Vehicle {
  final String name;
  Vehicle(this.name);

  void moveForward(int meters);
}

class Car extends Vehicle {
  final int passengers;
  Car(super.name, this.passengers);

  @override
  void moveForward(int meters) {
    // ...
  }
}

class MockVehicle implements Vehicle {
  @override
  String get name => 'mock';

  @override
  void moveForward(int meters) {
    // ...
  }
}

// Vehicle myVehicle = Vehicle('x'); // ERRO: abstract não instancia

// PRIMARY CONSTRUCTOR
abstract class Vehicle(final String name) {
  void moveForward(int meters);
}

class Car(super.name, final int passengers) extends Vehicle {
  @override
  void moveForward(int meters) {
    // ...
  }
}

// MockVehicle não tem construtor (só getter) → fica igual.
class MockVehicle implements Vehicle {
  @override
  String get name => 'mock';

  @override
  void moveForward(int meters) {
    // ...
  }
}
```

Abstract **sem campos** — o header não muda:

```dart
abstract class Shape {
  double get area;
}
```

---

### 3) `base`

Fora da library: pode construir e **estender**, não implementar. Subtipo (`extends` / `implements`) tem que ser `base`, `final` ou `sealed`. Garante que o construtor da base roda e membros privados existem no subtipo.

```dart
// HOJE
base class Vehicle {
  final String name;
  Vehicle(this.name);

  void moveForward(int meters) {
    // ...
  }
}

base class Car extends Vehicle {
  final int passengers;
  Car(super.name, this.passengers);
}

// base class MockVehicle implements Vehicle { ... }
// ERRO fora da library: base não pode ser implemented

// PRIMARY CONSTRUCTOR
base class Vehicle(final String name) {
  void moveForward(int meters) {
    // ...
  }
}

base class Car(super.name, final int passengers) extends Vehicle;
```

---

### 4) `interface`

Fora da library: pode construir e **implementar**, não estender.

```dart
// HOJE
interface class Vehicle {
  final String name;
  Vehicle(this.name);

  void moveForward(int meters) {
    // ...
  }
}

class MockVehicle implements Vehicle {
  @override
  final String name;
  MockVehicle(this.name);

  @override
  void moveForward(int meters) {
    // ...
  }
}

// class Car extends Vehicle { ... }
// ERRO fora da library: interface não pode ser extended

// PRIMARY CONSTRUCTOR
interface class Vehicle(final String name) {
  void moveForward(int meters) {
    // ...
  }
}

// MockVehicle tem `@override` no campo `name`. A documentação não mostra
// anotação em declaring parameter → mantém a implementação antiga.
class MockVehicle implements Vehicle {
  @override
  final String name;
  MockVehicle(this.name);

  @override
  void moveForward(int meters) {
    // ...
  }
}
```

---

### 5) `abstract interface` (interface pura)

Não instancia. Fora da library: só **implementar**, não estender. Pode ter membros abstratos.

```dart
// HOJE
abstract interface class Vehicle {
  String get name;
  void moveForward(int meters);
}

class MockVehicle implements Vehicle {
  @override
  final String name;
  MockVehicle(this.name);

  @override
  void moveForward(int meters) {
    // ...
  }
}

// Vehicle myVehicle = Vehicle(); // ERRO: abstract
// class Car extends Vehicle { ... } // ERRO fora da library

// PRIMARY CONSTRUCTOR
abstract interface class Vehicle {
  String get name;
  void moveForward(int meters);
}

// MockVehicle tem `@override` no campo `name`. A documentação não mostra
// anotação em declaring parameter → mantém a implementação antiga.
class MockVehicle implements Vehicle {
  @override
  final String name;
  MockVehicle(this.name);

  @override
  void moveForward(int meters) {
    // ...
  }
}
```

---

### 6) `final`

Fecha a hierarquia **fora** da library: não estender, não implementar. Dentro da mesma library pode. Subtipo na mesma library: `base`, `final` ou `sealed`. Padrão de widget/modelo que ninguém deve subclassar.

```dart
// HOJE
final class Vehicle {
  final String name;
  Vehicle(this.name);

  void moveForward(int meters) {
    // ...
  }
}

// class Car extends Vehicle { ... } // ERRO fora da library
// class MockVehicle implements Vehicle { ... } // ERRO fora da library

// PRIMARY CONSTRUCTOR
final class Vehicle(final String name) {
  void moveForward(int meters) {
    // ...
  }
}
```

`const` + `final` (imutável e hierarquia fechada) — padrão de widget:

```dart
// HOJE
final class ProductTile extends StatelessWidget {
  const ProductTile({
    super.key,
    required this.title,
    required this.price,
  });

  final String title;
  final double price;

  @override
  Widget build(BuildContext context) {
    return Text('$title R\$ $price');
  }
}

// PRIMARY CONSTRUCTOR
final class const ProductTile({
  super.key,
  required final String title,
  required final double price,
}) extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Text('$title R\$ $price');
  }
}
```

---

### 7) `sealed`

Implicitamente abstract. **Não** escrever `abstract sealed`. Não instancia. Subtipos só na **mesma** library. Switch exhaustivo. Pode ter factory e construtor para as subclasses usarem. Subclasses **não** são implicitamente abstract.

```dart
// HOJE
sealed class Vehicle {
  final String name;
  Vehicle(this.name);
}

class Car extends Vehicle {
  final int passengers;
  Car(super.name, this.passengers);
}

class Truck implements Vehicle {
  @override
  final String name;
  final double loadKg;
  Truck(this.name, this.loadKg);
}

class Bicycle extends Vehicle {
  Bicycle(super.name);
}

// Vehicle myVehicle = Vehicle('x'); // ERRO: sealed não instancia
Vehicle myCar = Car('Uno', 4);

extension VehicleSounds on Vehicle {
  String get sound {
    return switch (this) {
      Car() => 'vroom',
      Truck() => 'VROOOOMM',
      Bicycle() => 'ring',
    };
  }
}

// PRIMARY CONSTRUCTOR
sealed class Vehicle(final String name);

class Car(super.name, final int passengers) extends Vehicle;
// Truck tem `@override` no campo `name` → mantém a implementação antiga.
class Truck implements Vehicle {
  @override
  final String name;
  final double loadKg;
  Truck(this.name, this.loadKg);
}

class Bicycle(super.name) extends Vehicle;

extension VehicleSounds on Vehicle {
  String get sound {
    return switch (this) {
      Car() => 'vroom',
      Truck() => 'VROOOOMM',
      Bicycle() => 'ring',
    };
  }
}
```

Sealed nomeado privado + factory no body:

```dart
// HOJE
sealed class Result {
  const Result._();
  factory Result.ok(Object value) = Success;
  factory Result.err(String message) = Failure;
}

final class Success extends Result {
  final Object value;
  const Success(this.value) : super._();
}

final class Failure extends Result {
  final String message;
  const Failure(this.message) : super._();
}

// PRIMARY CONSTRUCTOR
sealed class const Result._() {
  factory Result.ok(Object value) = Success;
  factory Result.err(String message) = Failure;
}

// Success / Failure chamam `super._()`. A documentação não mostra
// superinitializer no `this :` → mantêm a implementação antiga.
final class Success extends Result {
  final Object value;
  const Success(this.value) : super._();
}

final class Failure extends Result {
  final String message;
  const Failure(this.message) : super._();
}
```

---

### 8) Combinações válidas

#### 8a) `abstract base`

Não instancia. Fora da library só **estender**. Subtipo `base` / `final` / `sealed`.

```dart
// HOJE
abstract base class Vehicle {
  final String name;
  Vehicle(this.name);
  void moveForward(int meters);
}

base class Car extends Vehicle {
  final int passengers;
  Car(super.name, this.passengers);

  @override
  void moveForward(int meters) {
    // ...
  }
}

// PRIMARY CONSTRUCTOR
abstract base class Vehicle(final String name) {
  void moveForward(int meters);
}

base class Car(super.name, final int passengers) extends Vehicle {
  @override
  void moveForward(int meters) {
    // ...
  }
}
```

#### 8b) `abstract final`

Não instancia. Hierarquia fechada fora da library. Subtipos só na mesma library, marcados `base` / `final` / `sealed`.

```dart
// HOJE
abstract final class Vehicle {
  final String name;
  Vehicle(this.name);
  void moveForward(int meters);
}

final class Car extends Vehicle {
  final int passengers;
  Car(super.name, this.passengers);

  @override
  void moveForward(int meters) {
    // ...
  }
}

// PRIMARY CONSTRUCTOR
abstract final class Vehicle(final String name) {
  void moveForward(int meters);
}

final class Car(super.name, final int passengers) extends Vehicle {
  @override
  void moveForward(int meters) {
    // ...
  }
}
```

#### 8c) `mixin class`

Pode construir, estender, implementar e mixar. Primary constructor só **trivial** (sem parâmetros / initializer / body).

```dart
// HOJE
mixin class Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
}

class Musician extends Musical {
  Musician();
}

class Performer with Musical {}

// PRIMARY CONSTRUCTOR
// Musical não tem construtor → fica igual.
mixin class Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
}

// `Musician();` vazio: a documentação não mostra `class Nome()` → mantém.
class Musician extends Musical {
  Musician();
}

// Performer não tem construtor → fica igual.
class Performer with Musical {}
```

#### 8d) `base mixin class`

Mixar/estender sim. Implementar não (fora da library).

```dart
// HOJE
base mixin class Musical {
  void play() {}
}

base class Musician extends Musical {
  Musician();
}

// PRIMARY CONSTRUCTOR
base mixin class Musical {
  void play() {}
}

// `Musician();` vazio: a documentação não mostra `class Nome()` → mantém.
base class Musician extends Musical {
  Musician();
}
```

#### 8e) `abstract mixin class`

Não instancia. Estender / implementar / mixar sim.

```dart
// HOJE
abstract mixin class Musical {
  void play();
}

class Musician with Musical {
  @override
  void play() {}
}

// PRIMARY CONSTRUCTOR
abstract mixin class Musical {
  void play();
}

// Musician não tem construtor → fica igual.
class Musician with Musical {
  @override
  void play() {}
}
```

#### 8f) `abstract base mixin class`

Não instancia. Estender / mixar sim. Implementar não.

```dart
// HOJE
abstract base mixin class Musical {
  void play();
}

base class Musician with Musical {
  @override
  void play() {}
}

// PRIMARY CONSTRUCTOR
abstract base mixin class Musical {
  void play();
}

// Musician não tem construtor → fica igual.
base class Musician with Musical {
  @override
  void play() {}
}
```

#### 8g) `mixin` / `base mixin`

Não são `class`. Sem primary constructor com parâmetros. `mixin` não instancia e não estende; só mixar (+ implementar se não for `base`).

```dart
// HOJE (= continua igual no 3.13)
mixin Musical {
  void play() {}
}

base mixin StrictMusical {
  void play() {}
}

class Band with Musical {}
base class Orchestra with StrictMusical {}

// PRIMARY CONSTRUCTOR — nada muda: mixin não tem primary constructor,
// e Band / Orchestra não têm construtor.
mixin Musical {
  void play() {}
}

base mixin StrictMusical {
  void play() {}
}

class Band with Musical {}
base class Orchestra with StrictMusical {}
```

---

## Lembrete — este é o padrão a adotar

Não entregue a forma antiga. Saída válida parece com isto:

```dart
class Car(super.name, final int passengers) extends Vehicle;
final class const ProductTile({super.key, required final String title}) extends StatelessWidget { ... }
```

---

# Tipos de primary constructor — referência para a alteração

**Use esta parte como referência na hora de transformar a classe.** Para cada classe:

1. Identifique o **tipo** de construtor/campo que ela tem hoje (tabela abaixo).
2. Abra a seção do tipo e aplique a **forma** mostrada.
3. Confira o **Pode / Não pode** do tipo e as **Regras gerais**. Se a classe cair em algum "Não pode", **não converta**: mantenha a implementação antiga (ver [Quando manter a implementação antiga](#quando-manter-a-implementação-antiga)) e avise.
4. Se o caso não estiver aqui, **não invente: mantenha o antigo e pergunte**.

## Quando manter a implementação antiga

Se a classe tiver qualquer item abaixo, **não converta**. Deixe o bloco **exatamente como está** (campos no body + construtor tradicional), para não quebrar, e avise qual item impediu:

| Situação na classe | Por quê (documentação) |
|---|---|
| Campo **`late`** inicializado pelo construtor (`late` + `this.x`) | Declaring parameter não pode ser `late`. **Se possível, mantenha a classe antiga.** Se não for possível, a forma documentada é: o campo `late` fica **declarado no body como hoje** ("declare the fields in the class body as usual") — sugira e pergunte antes. |
| Campo **`late`** que **não** vem do construtor (ex.: `late final x = ...` no body) | Pode converter os outros campos; o campo `late` fica no body exatamente como está. Parâmetros do header **não** podem ser usados em inicializador `late`. |
| Campo com **anotação** (ex.: `@override final String name;`) inicializado pelo construtor | A documentação não mostra anotação em declaring parameter. Manter a anotação = manter a implementação antiga. |
| Construtor que chama **`super(...)` / `super._()`** na initializer list | A documentação não mostra superinitializer no `this :`. Só `super.x` (super parameters) é documentado. |
| Construtor **vazio** `Nome();` | A documentação não mostra `class Nome()` com lista vazia. |
| Classe **sem construtor** | Nada a converter. Não adicione `()`. |
| Campo **`external`** inicializado pelo construtor | Declaring parameter não pode ser `external`. |
| Corpo do construtor com **`async`**, **`async*`**, **`sync*`** ou **`=>`** | O bloco `this` não aceita nenhum deles. |
| `const` com **corpo `{ }`** no construtor | Const primary constructor não pode ter bloco de corpo, nem vazio. |
| `const` com campo não-`final`, `late` ou não inicializado | Const exige todos `final`, sem `late`, definitivamente inicializados. |
| **Mais de um** construtor generativo não-redirecionador | Com primary constructor, não pode haver outro generativo não-redirecionador no body. |
| Parâmetro com **mesmo nome** de um método ou de outro campo do body | Erro de compilação. |
| Campo inicializado na declaração **e** no construtor/initializer list | Erro de compilação. |
| Initializer list que **atribui** a um parâmetro | Parâmetros são read-only no escopo de inicialização. |
| `covariant` em campo **`final`** | `covariant` só em declaring parameter `var`. |
| **`mixin class`** com construtor que tem parâmetros, initializer list ou corpo | Mixin class só aceita primary constructor sem nada disso. |
| Extension type com **mais de um** parâmetro | Extension type tem exatamente um parâmetro. |
| Parâmetros **opcionais `[ ]`** ou **valores default** | A documentação não cobre — manter e perguntar. |

Pode converter as **outras** classes do arquivo normalmente; só a classe com o problema fica no formato antigo.

### Se a conversão quebrar o código

1. **Prioridade:** manter a implementação antiga daquele bloco, sem mexer.
2. Só se um ajuste for **extremamente necessário** (ex.: a classe não compila sem ele), **não aplique sozinho**.
3. **Sugira a alteração e pergunte antes**, mostrando:
   - **Classe / bloco:** onde está.
   - **Problema:** o que quebra e qual regra da documentação impede.
   - **Sugestão:** o código atual e o código proposto, lado a lado.
   - **Impacto:** o que mais muda (chamadas, comportamento), se mudar.
4. Só aplique depois da confirmação.

Formato da sugestão:

````text
Classe: <Nome> (<arquivo>)
Problema: <o que quebra> — <regra da documentação>
Atual:
```dart
<código atual>
```
Sugestão:
```dart
<código proposto>
```
Impacto: <nenhum | o que muda>
Posso aplicar?
````

Tudo abaixo vem só da documentação oficial:

- [Primary constructors](https://dart.dev/language/primary-constructors)
- [Extension types](https://dart.dev/language/extension-types)

O que a documentação **não** cobre (ex.: valores default e parâmetros opcionais `[ ]` em primary constructor) não está aqui. **Nesses casos, pergunte.**

## Qual tipo usar

| Classe hoje tem… | Tipo |
|---|---|
| `this.x` com campo mutável | [1. Declaring `var`](#1-declaring-parameter-var--final) |
| `this.x` com campo `final` | [1. Declaring `final`](#1-declaring-parameter-var--final) |
| parâmetro que não vira campo | [2. Não-declarante](#2-parâmetro-não-declarante) |
| body vazio `{}` | [3. Body vazio `;`](#3-body-vazio-) |
| `: assert(...)` / initializer list / corpo `{ }` no construtor | [4. Bloco `this`](#4-bloco-this-initializer-list-e-ou-corpo) |
| `const Nome(...)` | [5. Const](#5-const) |
| `Nome.nome(...)` / `Nome._(...)` | [6. Nomeado](#6-construtor-nomeado--nomeado-privado) |
| named param privado `{required this._x}` | [7. Named param privado](#7-named-parameter-privado) |
| `super.x` | [8. Super parameters](#8-super-parameters) |
| enum com construtor | [9. Enum](#9-enum) |
| extension type | [10. Extension type](#10-extension-type) |
| mixin class | [11. Mixin class](#11-mixin-class) |

---

## 1) Declaring parameter (`var` / `final`)

```dart
class Point(var int x, var int y);
```

**Pode**
- `var` ou `final` no parâmetro do header → cria o campo (instance variable).
- `covariant` **só** em declaring parameter mutável (`var`).

**Não pode**
- Declaring parameter `late` ou `external`.
- Mesmo nome de um método ou de outro campo do body.
- `covariant` em parâmetro `final` ou não-declarante.

## 2) Parâmetro não-declarante

```dart
class User(String name);
```

**Pode**
- Parâmetro sem `var` / `final`: **não cria campo**, funciona como parâmetro de construtor tradicional.
- Ser usado em inicializador de campo não-`late` e na initializer list (ver 4).

**Não pode**
- Ser acessado como campo — não existe campo.

## 3) Body vazio `;`

```dart
class Point(var int x, var int y);
```

**Pode**
- Trocar `{}` vazio por `;` em class, mixin class, extension e extension type.

## 4) Bloco `this` (initializer list e/ou corpo)

```dart
class Point(var int x, var int y) {
  this : assert(x >= 0 && y >= 0) {
    print('Point initialized at ($x, $y)');
  }
}
```

Só initializer list (termina em `;`):

```dart
class Point(var int x, var int y) {
  this : assert(x >= 0);
}
```

Escopos:

```dart
class DeltaPoint(final int x, int delta) {
  final int y = x + delta;
}
```

```dart
class ScopingDemo(var String x, String suffix) {
  // Inicializador não-late: 'x' é o parâmetro.
  final String fieldAtDeclaration = x;
  final String fieldInInitializer;

  // Initializer list: 'x' é o parâmetro.
  this : fieldInInitializer = x {
    // No corpo: 'x' é o campo induzido.
    x = x.toUpperCase();
    // 'suffix' não gera campo, continua sendo o parâmetro.
    print('$x$suffix');
  }
}
```

**Pode**
- `this` + bloco **dentro** da classe, com initializer list, corpo, ou os dois.
- Usar os parâmetros do header em inicializadores de campo **não-`late`** e na initializer list.
- No corpo: declaring parameter → é o **campo**; não-declarante → é o **parâmetro**.

**Não pode**
- `this` sem primary constructor no header.
- Mais de **um** bloco `this` por classe.
- `async`, `async*`, `sync*` ou `=>` no bloco.
- Atribuir a parâmetro do header em inicializador de campo ou na initializer list (são read-only ali).
- Inicializar o mesmo campo na declaração **e** na initializer list.

## 5) Const

```dart
class const ConstPoint(final int x, final int y) {
  final int z;
  this : z = x + y;
}
```

**Pode**
- `const` antes do nome da classe no header.
- Initializer list terminando em `;`.

**Não pode**
- Bloco de corpo `{ }` no `this`, nem vazio.
- Campo que não seja `final`, ou campo `late`.
- Campo sem inicialização garantida (por declaring parameter, inicializador de campo ou initializer list).
- Expressão de inicialização que não seja potencialmente constante.

## 6) Construtor nomeado / nomeado privado

```dart
class Point.custom(var int x, var int y);
class Point._(var int x, var int y);
```

**Pode**
- `.nome` depois do nome da classe no header.
- Nome privado (`._`) para restringir a instanciação direta.

## 7) Named parameter privado

```dart
class User({required var String _name});
// uso: User(name: 'John Doe')
```

**Pode**
- Named parameter com `_`: quem chama usa o nome **sem** `_`.

## 8) Super parameters

```dart
class Person(final String name, final int age);

class Employee(super.name, super.age, final String role) extends Person;
```

**Pode**
- `super.x` no header, igual ao construtor tradicional (repassa para a superclasse).

## 9) Enum

```dart
enum Color(final String hex) {
  red('#FF0000'),
  green('#00FF00'),
  blue('#0000FF'),
}
```

**Pode**
- Primary constructor em enum — é **implicitamente const**.
- Omitir `const` (é redundante).

**Não pode**
- Outro construtor generativo **não-redirecionador** no body.

## 10) Extension type

```dart
extension type E(int i) {
  // Define set of operations.
}
```

**Pode**
- Exatamente **um** parâmetro, sempre declarante (mesmo sem `var` / `final`).
- Body vazio pode virar `;`.

**Não pode**
- Mais de um parâmetro.

## 11) Mixin class

**Pode**
- Primary constructor **só** se não tiver parâmetros, initializer list nem corpo.

**Não pode**
- Parâmetros, initializer list ou bloco `this` no primary constructor.
- Outro construtor generativo **não-redirecionador** no body.

---

## Regras gerais (documentação)

- Primary constructor exige language version **3.13+**.
- `final` / `var` em parâmetros de **outras** declarações (funções top-level, métodos, closures, construtores no body) é erro `extraneous_modifier`. Corrija removendo o modifier ou com `dart fix`.
- Para imutabilidade de parâmetros como estilo: lint `parameter_assignments`.
- Em 3.12 ou menor, antes de subir: lints `avoid_final_parameters` e `var_with_no_type_annotation` acham esses modifiers.

## Ordem do header

```text
[abstract] [base | interface | final | sealed] [mixin] class [const] Nome[.ctor](params)
  [extends Super] [with Mixin] [implements Iface]
```

Exemplos por class modifier: ver a seção **Class modifiers** acima.
