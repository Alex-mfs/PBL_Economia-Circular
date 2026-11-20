# TáNaValidade — Guia do UML (PlantUML)

Este documento explica **como abrir o diagrama de classes `.puml` no Visual Studio Code**, como interpretar a notação UML e como está organizado o modelo proposto para a aplicação **TáNaValidade**.


## 1. Visualizar o diagrama no VS Code

1. Abre o projeto no **Visual Studio Code**.
2. Abre **Extensions** (`Ctrl + Shift + X`) e instala **PlantUML**, de **jebbs** (`jebbs.plantuml`).
3. No explorador de ficheiros, abre o diagrama com a extensão **`.puml`**, dentro de `docs/uml/`.
4. Com o ficheiro aberto, prime **`Alt + D`** (**`Option + D`** no macOS). Em alternativa, abre a Command Palette (`Ctrl + Shift + P`) e procura **`PlantUML: Preview Current Diagram`**.
5. Mantém o código e a pré-visualização lado a lado para acompanhares as alterações.


## 2. Como ler uma classe

Exemplo retirado do projeto:

```plantuml
class Utilizador {
  -id: Integer
  -nome: String
  -passwordHash: String [0..1]
  -/totalPontos: Integer
  +atualizarPerfil(nome, email): void
}
```

| Elemento | Significado |
| --- | --- |
| `class Utilizador` | Define uma classe do modelo. |
| `-nome: String` | Atributo privado (`-`) do tipo `String`. |
| `+atualizarPerfil(...): void` | Operação pública (`+`) que não devolve um valor (`void`). |
| `Integer`, `Decimal`, `Boolean`, `Date`, `DateTime` | Tipos de dados usados nos atributos. |
| `[0..1]` após o tipo | Indicação textual de que o campo é opcional; pode estar ausente ou ter um valor. |
| `-/totalPontos: Integer` | Atributo privado e **derivado** (`/`): é calculado, neste caso, a partir de `MovimentoPontos`. |


## 3. O que significam `0`, `1` e `*`?

Os números colocados nas **extremidades das ligações** indicam a **multiplicidade**: quantas instâncias de uma classe podem estar associadas a uma instância da outra classe.

| Multiplicidade | Significado |
| --- | --- |
| `1` | Exatamente uma (obrigatória). |
| `0..1` | Zero ou uma (opcional). |
| `0..*` | Zero ou várias. |
| `1..*` | Uma ou várias (pelo menos uma). |
| `*` | Várias possíveis, incluindo zero (equivalente a `0..*`). |

### Exemplo A — uma habitação pode ter vários locais

```plantuml
Habitacao "1" -- "0..*" LocalArmazenamento : define locais
```

- Uma **Habitação** pode ter **zero ou vários** locais de armazenamento (frigorífico, despensa, congelador, etc.).
- Cada **LocalArmazenamento** está associado a **exatamente uma** habitação.


### Exemplo B — membros de uma habitação

```plantuml
Utilizador "1" -- "0..*" MembroHabitacao : participa através de
Habitacao "1" -- "1..*" MembroHabitacao : tem membros
```

- Um **Utilizador** pode participar em zero ou várias habitações, através de registos `MembroHabitacao`.
- Cada **MembroHabitacao** pertence a um utilizador e a uma habitação.
- Cada **Habitação** tem **pelo menos um** registo de membro (`1..*`).
- `MembroHabitacao` guarda ainda o **papel** (`papel`) e a **data de adesão** (`aderiuEm`).

### Exemplo C — convidados que ainda não aceitaram

```plantuml
Utilizador "0..1" -- "0..*" ConviteHabitacao : utilizador que aceitou
```

- Um convite pode ter **zero ou um** utilizador associado como a pessoa que aceitou.
- Um utilizador pode estar associado à aceitação de vários convites.
- Antes da aceitação, o utilizador que aceitou pode ainda não estar definido. O remetente é representado por **outra ligação**, com multiplicidade `1` junto a `Utilizador`.

### Exemplo D — receitas e ingredientes

```plantuml
Receita "1" *-- "0..*" IngredienteReceita : contém
```

- Uma **Receita** pode ter zero ou vários registos `IngredienteReceita` no modelo geral.
- Cada **IngredienteReceita** pertence a uma única receita.
- O símbolo **`*--`** significa **composição**: o ingrediente registado é uma parte da receita e depende dela para existir.
- **Regra de negócio adicional:** uma receita só deve ser **publicada** se tiver **pelo menos um ingrediente**. Por isso, `0..*` é compatível com uma receita ainda em rascunho.

## 4. Tipos de ligação

| Notação PlantUML | Tipo | Utilização neste diagrama |
| --- | --- | --- |
| `--` | Associação | Liga entidades relacionadas, por exemplo, `Produto` e `AlimentoRegistado`. |
| `*--` | Composição | Representa uma relação todo–parte, usada em `Receita`–`IngredienteReceita` e `Receita`–`FotoReceita`. |
| `: texto` | Nome da relação | Explica o significado da ligação, como `guarda`, `autor` ou `recebe gostos`. |

Uma linha `--` não indica, por si só, herança nem a direção em que uma classe «chama» a outra. A posição no desenho também não altera a multiplicidade.

## 5. Organização do nosso UML

O modelo está dividido em **quatro grupos** (`package`) para facilitar a leitura.

### 5.1. Utilizadores e casas

| Classe | Responsabilidade |
| --- | --- |
| `Utilizador` | Dados da conta, preferências de alertas e total de pontos calculado. |
| `AutenticacaoExterna` | Identidades de início de sessão através de fornecedores externos. |
| `Habitacao` | Casa ou espaço partilhado pelos utilizadores. |
| `MembroHabitacao` | Ligação entre utilizador e habitação, incluindo papel e data de adesão. |
| `ConviteHabitacao` | Convites enviados para entrar numa habitação, com estado e datas. |

**Fluxo principal:** um utilizador participa numa habitação por meio de `MembroHabitacao`. A habitação pode ter vários membros e convites; um convite pode ser aceite ou recusado.

### 5.2. Gestão de alimentos

| Classe | Responsabilidade |
| --- | --- |
| `LocalArmazenamento` | Local definido dentro de uma habitação (por exemplo, frigorífico). |
| `CategoriaProduto` | Agrupamento de produtos por categoria. |
| `Produto` | Identificação de um produto, incluindo nome e código de barras opcional. |
| `AlimentoRegistado` | Quantidade, unidade, validade, estado e histórico básico de um alimento guardado. |

**Fluxo principal:** uma habitação guarda alimentos, identificados por um `Produto` e registados por um `Utilizador`. Cada alimento pode estar associado a um `LocalArmazenamento` (ligação opcional `0..1`). Um produto pode aparecer em vários registos de alimento.

**Distinção importante:** `Produto` é a definição do produto; `AlimentoRegistado` é uma ocorrência desse produto numa habitação, com quantidade e prazo próprios.

### 5.3. Receitas e comunidade

| Classe | Responsabilidade |
| --- | --- |
| `Receita` | Conteúdo, preparação, tempo, doses, autor e estado de publicação. |
| `IngredienteReceita` | Produto, quantidade e unidade exigidos numa receita. |
| `FotoReceita` | Fotografias associadas a uma receita. |
| `ComentarioReceita` | Comentários escritos por utilizadores. |
| `GostoReceita` | Registos de gostos atribuídos às receitas. |

**Fluxo principal:** um utilizador cria receitas, às quais adiciona ingredientes, fotografias e, se aplicável, publica. Outros utilizadores podem comentar e atribuir gostos. Cada ingrediente refere-se a um `Produto`.

### 5.4. Pontos, medalhas e notificações

| Classe | Responsabilidade |
| --- | --- |
| `MovimentoPontos` | Regista a atribuição ou alteração de pontos, motivo e data. |
| `Medalha` | Define medalhas e critérios. |
| `UtilizadorMedalha` | Regista que utilizador conquistou determinada medalha e quando. |
| `Notificacao` | Mensagens destinadas a utilizadores, com data de criação e leitura opcional. |

**Fluxo principal:** os pontos do utilizador são calculados a partir dos movimentos. Alguns movimentos podem ter origem numa receita ou numa fotografia; as notificações podem estar relacionadas com alimentos. As medalhas são atribuídas através de `UtilizadorMedalha`.

## 6. Algumas relações importantes do projeto

| Relação | Como interpretar |
| --- | --- |
| `CategoriaProduto "1" -- "0..*" Produto` | Cada produto tem uma categoria; uma categoria pode classificar vários produtos. |
| `Produto "1" -- "0..*" AlimentoRegistado` | Cada alimento registado identifica um produto; o mesmo produto pode surgir em vários registos. |
| `LocalArmazenamento "0..1" -- "0..*" AlimentoRegistado` | O local de um alimento é opcional; um local pode conter vários alimentos. |
| `Utilizador "1" -- "0..*" Receita` | Cada receita tem um autor; um utilizador pode criar várias receitas. |
| `Receita "1" -- "0..*" ComentarioReceita` | Uma receita pode receber vários comentários; cada comentário pertence a uma receita. |
| `Utilizador "1" -- "0..*" GostoReceita` | Cada gosto foi atribuído por um utilizador; um utilizador pode atribuir vários gostos. |
| `Utilizador "1" -- "0..*" MovimentoPontos` | Cada movimento pertence a um utilizador; um utilizador pode ter vários movimentos. |
| `Utilizador "1" -- "0..*" Notificacao` | Cada notificação tem um destinatário; um utilizador pode receber várias notificações. |
| `AlimentoRegistado "0..1" -- "0..*" Notificacao` | Uma notificação pode estar associada a um alimento ou não; um alimento pode originar várias notificações. |


## 7. Como contribuir para o UML

1. Edita o ficheiro `.puml` existente em `docs/uml/`.
2. Atualiza as classes, atributos, métodos ou relações necessários.
3. Abre a pré-visualização (`Alt + D`) e confirma que o diagrama continua legível.
4. Se mudares uma regra de negócio, atualiza também esta documentação.
5. Guarda o ficheiro, faz commit e partilha a alteração com a equipa para revisão.

### Resumo rápido

- **Classe:** representa uma entidade/conceito da aplicação.
- **Atributo:** informação que essa classe guarda.
- **Método:** operação que essa classe pode realizar.
- **`0..1`:** associação ou campo opcional (no caso dos atributos, está escrito como indicação textual).
- **`1`:** exatamente uma instância associada.
- **`0..*`:** nenhuma ou várias instâncias associadas.
- **`1..*`:** pelo menos uma instância associada.
- **`--`:** associação simples.
- **`*--`:** composição, com dependência entre a parte e o todo.

**O objetivo deste UML é criar uma visão partilhada da estrutura do TáNaValidade antes de transformar o modelo em código e tabelas da base de dados.**
