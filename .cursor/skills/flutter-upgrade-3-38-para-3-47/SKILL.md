---
name: flutter-upgrade-3-38-para-3-47
description: >-
  Verifica o salto Flutter 3.38.10 para 3.47.7 com o catálogo de breaking
  changes, acessibilidade, Material, Cupertino, Gradle e Swift Package Manager.
  Use quando o usuário pedir impacto de upgrade Flutter nesse intervalo,
  widgets depreciados, containsSemantics, headingLevel, material_ui, Kotlin
  embutido, UIScene ou CocoaPods. A fase do catálogo não audita aplicativo.
---

# Upgrade Flutter 3.38.10 para 3.47.7

Ler [docs/flutter-3.38.10-to-3.47.7-verificacao.md](../../../docs/flutter-3.38.10-to-3.47.7-verificacao.md) antes de concluir qualquer coisa sobre este salto. O catálogo é a fonte. Esta skill só fixa como aplicá-lo.

## Fase atual

O catálogo não avalia aplicativo, `pubspec`, plugin instalado nem design system local. Sem pedido explícito de fase posterior, responder a partir dos registros. Não abrir `lib/`, `android/`, `ios/`, `macos/` nem `pubspec.yaml` de um app para “conferir impacto”.

O projeto `lk` é a pasta da ferramenta. O código de exemplo gerado pelo Flutter nele não é alvo da verificação.

## Regras

1. Destino do salto: Flutter 3.47.7, Dart 3.13. Parar em 3.47.0 deixa A11Y-015 e A11Y-016 sem correção. A 3.47.8 não muda a lista de breaking changes.
2. `package:material_ui` e `package:cupertino_ui` são opt-in (DS-006). Não tratar como migração obrigatória. Manter `package:flutter/material.dart` e `package:flutter/cupertino.dart`.
3. Guia oficial reescrito com import `package:material_ui` ou `package:cupertino_ui` vale, neste salto, com o import do framework.
4. Widget que só lê `Theme`, `ColorScheme` ou `TextStyle` herda o registro `contagem: unica_no_ds`. Não abrir achado por tela.
5. Itens BASE e A11Y-004 já valem em 3.38.10. Não listá-los como novidade da 3.41+.
6. Itens da seção “Fora do stable 3.47.7” não entram no plano de migração deste salto.
7. `dart fix` cobre A11Y-002. Não cobre API-008 (`describeEnum`), API-003 (`onReorder`) nem API-007 (`IconData`).

## Fase posterior

Só começa quando o usuário indicar um repositório alvo.

1. Ler o catálogo inteiro de novo.
2. Seguir a ordem do plano de mitigação: compilação e abertura, acessibilidade silenciosa, design system numa passagem, avisos, condicionais.
3. Buscar os `simbolos` de cada registro uma vez. Agrupar pelo `id`. Vários arquivos do mesmo componente de tema viram um achado.
4. Não inventar pacote pub.dev. Classes de dependência estão na seção própria do catálogo.
5. Reportar só registros com ocorrência, mais os de plataforma que dependem de arquivo nativo encontrado. Citar o `id`.

Formato do relatório da fase posterior:

```markdown
# Impacto Flutter 3.38.10 para 3.47.7

## Achados
- id: A11Y-001
  contagem: por_ocorrencia
  onde: caminho
  mitigacao: uma linha do catálogo

## Sem ocorrência
- ids verificados e ausentes

## Fora deste salto
- DS-006 e demais opt-in, se alguém pedir para migrar agora
```
