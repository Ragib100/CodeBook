# ICPC Team Notebook

## Generating the PDF (with Docker)

> **No Docker installed?** Download and install [Docker Desktop](https://docs.docker.com/get-docker/) for your platform (Windows, macOS, or Linux). Docker Compose is included with Docker Desktop. On Linux you can also install [Docker Engine](https://docs.docker.com/engine/install/) and [Docker Compose](https://docs.docker.com/compose/install/) separately.

1. Pull the TeX Live Docker image:

```bash
docker pull texlive/texlive:latest
```

2. If you have [Docker](https://docs.docker.com/get-docker/) and
   [Docker Compose](https://docs.docker.com/compose/install/) installed, the PDF
   build is fully reproducible on any machine without installing TeX Live or
   patching Python packages:

```bash
docker compose build
docker compose run --rm build
```

After the run, **only `notebook.pdf` appears in the project root**. All the
intermediate LaTeX artifacts (`contents.tex`, `notebook.aux`, `notebook.log`,
`notebook.out`, `notebook.toc`, and the `_minted/` highlight cache) live in a
Docker-managed named volume (`mist_titan_codebook_codebook-build`), never on the
host.

> **Note:** the first time you run this on a fresh checkout, create an empty
> `notebook.pdf` so Docker bind-mounts it as a file (rather than creating it
> as a directory, which `pdflatex` cannot write to):
>
> ```bash
> touch notebook.pdf
> docker compose run --rm build
> ```

To clean up the named volume and free disk space:

```bash
docker compose down --volumes
```

If you'd rather drive Docker by hand, the equivalent command is:

```bash
docker build -t mist-codebook .
docker run --rm \
    -v "$(pwd)/code:/work/code" \
    -v "$(pwd)/math:/work/math" \
    -v "$(pwd)/images:/work/images" \
    -v "$(pwd)/notebook.pdf:/work/notebook.pdf" \
    -v codebook-build:/var/build \
    mist-codebook bash -c '
        mkdir -p /var/build
        export OUTPUT_DIRECTORY=/var/build
        python3 /work/generate_pdf.py
        cp /var/build/notebook.pdf /work/notebook.pdf
    '
```

The Docker image bundles:

- The full TeX Live distribution (matching `notebook.tex`'s package list).
- `latexminted==0.7.1` (the version compatible with TeX Live 2026's
  `minted.sty`, which already includes the upstream Python 3.14 fix from
  minted issues #463/#464 — no patching needed in the image).

## Acknowledgments

The Python script is a fork of the Stanford ICPC team's notebook generator.
