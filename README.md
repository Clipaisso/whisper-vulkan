# whisper-vulkan

Builds de **[whisper.cpp](https://github.com/ggml-org/whisper.cpp)** com o backend **Vulkan** para Windows x64, mantidos pela **[Clipaisso](https://clipaisso.com)** para uso no app **Clipaisso Desktop**.

> Este repositório **não** modifica o whisper.cpp — apenas o **compila** com o backend Vulkan habilitado e publica o binário pronto numa release, já que o projeto oficial não distribui esse build para Windows.

## Por que ele existe

O Clipaisso Desktop transcreve áudio localmente (ferramenta de legendas) usando whisper.cpp. As releases oficiais do whisper.cpp para Windows só trazem builds de:

- **CPU** — funciona em qualquer máquina, porém lento; e
- **CUDA** — aceleração por GPU, mas **exclusiva da NVIDIA**.

Ou seja, **não há build de GPU para AMD ou Intel**. Quem tem placa AMD/Intel ficava preso na CPU.

O backend **Vulkan** resolve isso: roda em **qualquer GPU** (AMD, NVIDIA e Intel) precisando apenas do **driver de vídeo** instalado — sem CUDA, sem ROCm, sem SDK do lado do usuário. Como o whisper.cpp oficial não publica esse build, a Clipaisso o compila aqui e o app o baixa sob demanda, exatamente como já faz com os binários de CPU e CUDA.

## O que tem na release

A cada execução, o workflow publica o asset **`whisper-vulkan-bin-x64.zip`** na release de tag fixa **`whisper-vulkan-latest`** (a URL nunca muda; o conteúdo é substituído):

```
https://github.com/ClipaIsso/whisper-vulkan/releases/download/whisper-vulkan-latest/whisper-vulkan-bin-x64.zip
```

O zip contém o `whisper-cli.exe` e as DLLs de runtime, incluindo a **`ggml-vulkan.dll`** (o backend Vulkan). A `vulkan-1.dll` **não** é incluída de propósito — ela é fornecida pelo driver da GPU no Windows.

## Requisito de runtime

Uma GPU com driver compatível com **Vulkan** — padrão em praticamente todo Windows 10/11 atual (AMD, NVIDIA e Intel). Nenhum SDK é necessário.

## Como (re)gerar o build

O build roda inteiramente nos runners do GitHub — nada precisa ser instalado localmente.

1. Aba **Actions** → **"Build whisper.cpp (Vulkan)"** → **Run workflow**.
2. Informe a tag do whisper.cpp em `whisper_ref` (ex.: `v1.8.6`).
3. Ao terminar, a release `whisper-vulkan-latest` é atualizada com o novo zip.

Por baixo, o workflow (`.github/workflows/build.yml`):

- faz checkout do `ggml-org/whisper.cpp` na tag escolhida;
- instala o Vulkan SDK (apenas para compilar os shaders);
- compila com `-DGGML_VULKAN=ON -DBUILD_SHARED_LIBS=ON`;
- empacota o `whisper-cli.exe` + DLLs (incluindo `ggml-vulkan.dll`) e publica a release.

## Licença e créditos

O whisper.cpp é de autoria da **ggml-org** sob licença **MIT** — todo o código compilado aqui pertence a esse projeto. Este repositório apenas redistribui builds binários por conveniência. Consulte a licença original em [ggml-org/whisper.cpp](https://github.com/ggml-org/whisper.cpp/blob/master/LICENSE).

---

Mantido por **[Clipaisso](https://clipaisso.com)**.
