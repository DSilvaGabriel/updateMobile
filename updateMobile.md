# Guia: Transformando projetos Angular em aplicativos Android com Capacitor

**Tecnologias:** Angular + Capacitor + Android Studio  
**Objetivo:** Converter um projeto Angular existente em um aplicativo Android, reutilizando sua estrutura, componentes, serviços e estilos.

---

## 1. Introdução

O Capacitor permite transformar aplicações web desenvolvidas com Angular em aplicativos nativos para Android e iOS.

Ele funciona como uma ponte entre a aplicação web e os recursos do sistema operacional.

A principal vantagem é que não precisamos reconstruir a aplicação utilizando Java, Kotlin ou Swift.

Podemos continuar desenvolvendo normalmente com:

- Angular;
- TypeScript;
- HTML;
- SCSS;
- Serviços e componentes;
- APIs e integrações existentes.

O Capacitor utiliza os arquivos compilados do Angular para alimentar a interface do aplicativo.

## 2. Pré-requisitos

Antes de começar, precisamos ter:

- Node.js e npm instalados;
- Projeto Angular funcionando;
- Angular CLI instalado ou disponível pelo projeto;
- Android Studio instalado;
- Android SDK configurado;
- JDK compatível com a versão do Android Gradle Plugin utilizada.

Também é recomendado ter um dispositivo Android com depuração USB habilitada para realizar testes físicos.

## 3. Acessar a pasta do projeto

Abra o PowerShell ou terminal do VS Code.

Entre na pasta raiz do projeto Angular.

Exemplo:

```powershell
cd D:\trabalho\meu-projeto
```

Confira se está na pasta correta:

```powershell
dir
```

Você deverá encontrar arquivos e pastas semelhantes a:

```text
meu-projeto/
├── src/
├── angular.json
├── package.json
├── tsconfig.json
└── node_modules/
```

**Importante:** todos os próximos comandos devem ser executados dentro da pasta raiz do projeto, nunca em `C:\Windows\system32`.

## 4. Instalar o Capacitor

Instale o núcleo do Capacitor:

```powershell
npm install @capacitor/core
```

Instale a CLI como dependência de desenvolvimento:

```powershell
npm install -D @capacitor/cli
```

Esses pacotes permitem configurar e gerenciar a integração entre Angular e Capacitor.

## 5. Inicializar o Capacitor

Execute:

```powershell
npx cap init
```

O terminal solicitará duas informações.

### Nome do aplicativo

Exemplo:

```text
Meu Projeto
```

Esse é o nome amigável que será exibido no dispositivo.

### Package ID

Exemplo:

```text
br.com.minhaempresa.meuprojeto
```

O identificador deve seguir o formato de domínio reverso e ser exclusivo para cada aplicativo.

Outros exemplos:

| Projeto | Package ID |
|---|---|
| Meu-Escola | br.com.meuescola.app |
| GamesZero | br.com.gameszero.app |
| Vitrine de Achados | br.com.vitrine.app |

Após preencher as informações, será criado o arquivo:

```text
capacitor.config.ts
```

## 6. Compilar o projeto Angular

Antes de integrar o Android, precisamos gerar os arquivos de produção do Angular.

Execute:

```powershell
ng build
```

O Angular criará uma pasta chamada `dist`.

O caminho pode variar conforme o nome do projeto e a configuração do Angular.

Exemplo:

```text
dist/
└── gamer-portal/
    └── browser/
        ├── index.html
        ├── main.js
        ├── styles.css
        └── ...
```

### Como descobrir a localização do index.html?

Execute:

```powershell
Get-ChildItem .\dist -Recurse -Filter index.html
```

Esse comando localizará o arquivo de entrada da aplicação compilada.

**Atenção:** o diretório que precisamos configurar é a pasta que contém o `index.html`, não necessariamente a pasta principal `dist`.

## 7. Configurar o capacitor.config.ts

Abra o arquivo `capacitor.config.ts`, localizado na raiz do projeto.

Configure conforme o caminho encontrado anteriormente.

Exemplo utilizado no GamesZero:

```typescript
import type { CapacitorConfig } from '@capacitor/cli';

const config: CapacitorConfig = {
  appId: 'br.com.gameszero.app',
  appName: 'GamesZero',
  webDir: 'dist/gamer-portal/browser'
};

export default config;
```

### Entendendo as propriedades

| Propriedade | Função |
|---|---|
| appId | Identificador exclusivo do aplicativo |
| appName | Nome exibido no dispositivo |
| webDir | Diretório dos arquivos compilados do Angular |

**Importante:** o valor de `webDir` deve ser adaptado para cada projeto.

Não copie automaticamente o caminho de outro projeto.

## 8. Instalar o suporte Android

Instale a plataforma Android:

```powershell
npm install @capacitor/android
```

Agora adicione o projeto nativo:

```powershell
npx cap add android
```

O Capacitor criará uma pasta chamada `android`.

Estrutura esperada:

```text
meu-projeto/
├── android/
├── src/
├── dist/
├── node_modules/
├── angular.json
├── package.json
└── capacitor.config.ts
```

Essa pasta contém o projeto nativo que será aberto pelo Android Studio.

**Observação:** execute `npx cap add android` somente na primeira configuração. Não é necessário repetir o comando a cada atualização.

## 9. Sincronizar Angular com Android

Depois de adicionar o Android, precisamos copiar os arquivos compilados do Angular para o projeto nativo.

Execute:

```powershell
ng build
```

Em seguida:

```powershell
npx cap sync android
```

O comando `sync` realiza duas operações principais:

1. Copia os arquivos compilados do Angular para o projeto Android.
2. Atualiza as configurações e os plugins do Capacitor.

Um resultado esperado é:

```text
√ Copying web assets
√ Creating capacitor.config.json
√ Updating Android plugins
√ update android

[info] Sync finished
```

Se a sincronização terminar sem erros, a integração foi realizada corretamente.

## 10. Abrir o Android Studio

Execute:

```powershell
npx cap open android
```

O Android Studio abrirá o projeto nativo.

Aguarde a sincronização do Gradle terminar.

Depois:

1. Conecte o celular por USB.
2. Ative as opções de desenvolvedor.
3. Habilite a depuração USB.
4. Autorize a conexão no celular.
5. Selecione o dispositivo no Android Studio.
6. Clique em Run (▶).

Também é possível executar em um emulador Android.

## 11. Gerar APK pelo terminal

Outra possibilidade é gerar o APK diretamente pelo Gradle.

Depois de sincronizar o projeto, execute:

```powershell
cd android
.\gradlew.bat assembleDebug
```

O APK de depuração normalmente será gerado em:

```text
android/app/build/outputs/apk/debug/app-debug.apk
```

Esse arquivo pode ser instalado em um dispositivo Android para testes.

Para gerar uma versão de distribuição, utilize o fluxo de assinatura e geração de AAB ou APK de release pelo Android Studio ou Gradle.

Não distribua uma versão debug como versão final de produção.

---

## 12. Como atualizar o aplicativo depois de modificar o Angular?

Essa é uma das partes mais importantes do processo.

Sempre que modificar o projeto Angular, será necessário gerar uma nova compilação e sincronizar os arquivos com o Android.

O procedimento é:

```powershell
ng build
npx cap sync android
```

Depois, abra ou atualize o projeto no Android Studio e execute novamente.

Não é necessário adicionar o Android novamente.

### Exemplo prático

Imagine que você alterou:

- A página inicial;
- O menu;
- O sistema de notícias;
- Os componentes;
- O SCSS;
- Os serviços de comunicação com a API.

Todas essas alterações serão incorporadas à próxima compilação e sincronização.

---

## 13. Automatizando o processo

Podemos criar comandos personalizados no `package.json`.

Dentro da propriedade `scripts`, adicione:

```json
{
  "mobile:build": "ng build && cap sync android",
  "mobile:open": "cap open android"
}
```

Assim, em vez de executar dois comandos separadamente, podemos utilizar:

```powershell
npm run mobile:build
```

Para abrir o Android Studio:

```powershell
npm run mobile:open
```

Se já existirem outros scripts no projeto, preserve-os e apenas acrescente os novos.

---

## 14. Problemas comuns e soluções

### Erro: The web assets directory must contain an index.html file

**Possível causa:** o `webDir` está apontando para o diretório incorreto.

**Solução:**

Localize o `index.html`:

```powershell
Get-ChildItem .\dist -Recurse -Filter index.html
```

Depois, corrija o `webDir` no `capacitor.config.ts`.

### Erro: ENOENT capacitor.plugins.json

**Possível causa:** a primeira sincronização falhou e os arquivos necessários não foram criados.

**Solução:**

Corrija o `webDir`, compile e sincronize novamente:

```powershell
ng build
npx cap sync android
```

### O aplicativo não apresenta as alterações mais recentes

**Possível causa:** o projeto Android ainda está utilizando os arquivos de uma compilação anterior.

**Solução:**

```powershell
ng build
npx cap sync android
```

Depois, execute novamente o aplicativo.

### O aplicativo abre, mas não carrega os dados da API

Verifique:

- Se a API está acessível pela internet;
- Se os endereços utilizados não apontam para `localhost`;
- Se as configurações de CORS permitem a origem utilizada;
- Se as credenciais e tokens estão sendo enviados corretamente;
- Se há problemas de conexão ou certificados HTTPS.

### Avisos do npm durante a instalação

Avisos de auditoria ou scripts de dependências não significam necessariamente que a instalação falhou.

Analise os avisos antes de executar correções como:

```powershell
npm audit fix --force
```

Evite aplicar atualizações forçadas sem verificar possíveis incompatibilidades.

---

## 15. Boas práticas para trabalhar com vários projetos

Cada projeto deve ter sua própria configuração do Capacitor.

Exemplo:

```text
Projetos/
├── meu-escola/
│   ├── android/
│   └── capacitor.config.ts
│
├── gameszero/
│   ├── android/
│   └── capacitor.config.ts
│
└── vitrine-afiliados/
    ├── android/
    └── capacitor.config.ts
```

Recomendações:

- Não copie a pasta `android` de um projeto para outro.
- Utilize um `appId` exclusivo por aplicativo.
- Configure corretamente o `appName`.
- Confira o `webDir` de cada aplicação.
- Mantenha versões compatíveis dos pacotes do Capacitor.
- Não adicione novamente a plataforma Android se ela já existir.
- Utilize HTTPS nas comunicações com APIs.
- Teste a responsividade em dispositivos reais.

---

## 16. Checklist reutilizável

Use esta lista sempre que for transformar um novo projeto Angular em aplicativo Android.

- [ ] Entrar na pasta raiz do projeto.
- [ ] Verificar se o Angular está funcionando.
- [ ] Instalar `@capacitor/core`.
- [ ] Instalar `@capacitor/cli`.
- [ ] Executar `npx cap init`.
- [ ] Definir o nome do aplicativo.
- [ ] Definir um Package ID exclusivo.
- [ ] Executar `ng build`.
- [ ] Localizar o `index.html` dentro de `dist`.
- [ ] Configurar o `webDir` no `capacitor.config.ts`.
- [ ] Instalar `@capacitor/android`.
- [ ] Executar `npx cap add android`.
- [ ] Executar `ng build`.
- [ ] Executar `npx cap sync android`.
- [ ] Abrir com `npx cap open android`.
- [ ] Aguardar a sincronização do Gradle.
- [ ] Executar no emulador ou celular físico.
- [ ] Testar navegação, imagens, APIs e responsividade.

---

## 17. Referências oficiais

- Capacitor: https://capacitorjs.com/docs
- Instalação: https://capacitorjs.com/docs/getting-started
- Android: https://capacitorjs.com/docs/android
- Android Studio: https://developer.android.com/studio

---

**Documentação criada a partir da integração do GamesZero com Capacitor.**

**Autor:** Gabriel Silva Fernandes  
**Organização:** Spreti Col  
**Tecnologias:** Angular, TypeScript, Capacitor e Android Studio

*Este procedimento pode ser reutilizado em outros projetos Angular, adaptando o nome, Package ID e caminho de compilação de cada aplicação.*