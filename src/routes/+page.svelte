<script lang="ts">
  import type { FormEventHandler } from 'svelte/elements'

  import Alic3 from '$lib/Alic3.svelte'

  interface Color {
    name: string
    secondaryName?: string
    hex: string
  }

  const defaultColors: Color[] = [
    {
      name: 'Background',
      hex: '#0c1721',
    },
    {
      name: 'Foreground',
      hex: '#ECF8FA',
    },
    {
      name: 'Cursor',
      hex: '#CCD8DA',
    },

    {
      name: 'Color0',
      secondaryName: 'Black',
      hex: '#0c1721',
    },
    {
      name: 'Color1',
      secondaryName: 'Red',
      hex: '#CCAAAA',
    },
    {
      name: 'Color2',
      secondaryName: 'Green',
      hex: '#AACCAA',
    },
    {
      name: 'Color3',
      secondaryName: 'Yellow',
      hex: '#CCCCAA',
    },
    {
      name: 'Color4',
      secondaryName: 'Blue',
      hex: '#AAAACC',
    },
    {
      name: 'Color5',
      secondaryName: 'Indigo',
      hex: '#CCAACC',
    },
    {
      name: 'Color6',
      secondaryName: 'Cyan',
      hex: '#AACCCC',
    },
    {
      name: 'Color7',
      secondaryName: 'White',
      hex: '#ECF8FA',
    },

    {
      name: 'Color8',
      secondaryName: 'Black Bold',
      hex: '#1c2731',
    },
    {
      name: 'Color9',
      secondaryName: 'Red Bold',
      hex: '#EEAAAA',
    },
    {
      name: 'Color10',
      secondaryName: 'Green Bold',
      hex: '#AAEEAA',
    },
    {
      name: 'Color11',
      secondaryName: 'Yellow Bold',
      hex: '#EEEEAA',
    },
    {
      name: 'Color12',
      secondaryName: 'Blue Bold',
      hex: '#AAAAEE',
    },
    {
      name: 'Color13',
      secondaryName: 'Indigo Bold',
      hex: '#EEAAEE',
    },
    {
      name: 'Color14',
      secondaryName: 'Cyan Bold',
      hex: '#AAEEEE',
    },
    {
      name: 'Color15',
      secondaryName: 'White Bold',
      hex: '#F6FCFF',
    },
  ]

  let colors: Color[] = [...defaultColors.map((color: Color): Color => ({ ...color }))]

  const getOnUpdateColor = (index: number): FormEventHandler<HTMLInputElement> => {
    return (event): void => {
      const potentialColor: string = event.currentTarget.value

      if (
        !potentialColor.startsWith('#') ||
        potentialColor.length !== 7 ||
        potentialColor
          .substring(1)
          .split('')
          .find((char: string): boolean => !/[0-9a-fA-F]/.test(char))
      ) {
        return
      }

      colors[index].hex = potentialColor
    }
  }

  const onExportClicked: () => void = (): void => {
    var blob = new Blob(
      [
        `# Theme file from https://themeish.alic3.dev/

${colors.map((color: Color): string => `${color.secondaryName ? `# ${color.secondaryName}\n` : ''}${color.name.toLowerCase()}=${color.hex}`).join('\n')}
`,
      ],
      { type: 'octet/stream' },
    )
    var url = window.URL.createObjectURL(blob)

    const downloadAnchor = document.createElement('a')
    document.body.appendChild(downloadAnchor)
    downloadAnchor.style.display = 'none'
    downloadAnchor.href = url
    downloadAnchor.download = 'themeish.theme'
    downloadAnchor.click()
    document.body.removeChild(downloadAnchor)
    window.URL.revokeObjectURL(url)
  }

  const resetColors: () => void = (): void => {
    colors = [...defaultColors.map((color: Color): Color => ({ ...color }))]
  }
</script>

<header class="header">
  <Alic3 header />

  <h1 class="heading">Themeish</h1>
</header>

<main class="main">
  <div class="color-blocks-container">
    {#each colors as color}
      <div
        class="color-block"
        style={`background: ${color.hex}`}
        aria-label={`${color.name}: ${color.hex}`}
      ></div>
    {/each}
  </div>

  <div class="color-text-blocks-container first">
    {#each colors as color}
      <div
        class="color-text-block"
        style={`background: ${color.hex}`}
        aria-label={`${color.name}: ${color.hex}`}
      >
        0:33
      </div>
    {/each}
  </div>

  <div class="color-text-blocks-container">
    {#each colors as color}
      <div
        class="color-text-block"
        style={`color: ${color.hex}; border: 1px solid ${color.hex}; background: ${colors[0].hex}`}
        aria-label={`${color.name}: ${color.hex}`}
      >
        0:33
      </div>
    {/each}
  </div>

  <div class="color-fields-container">
    <div>
      <button on:click={onExportClicked}>Export</button>
      <button on:click={resetColors}>Reset</button>
    </div>

    {#each colors as color, i}
      <fieldset class="color-field">
        <legend class="color-field-legend">
          {color.name}{color.secondaryName && `: ${color.secondaryName}`}
        </legend>
        <div class="color-inputs">
          <input type="text" value={color.hex} on:input={getOnUpdateColor(i)} />
          <input type="color" value={color.hex} on:input={getOnUpdateColor(i)} />
        </div>
      </fieldset>
    {/each}
  </div>
</main>

<style>
  .header {
    flex-shrink: 0;

    display: flex;
    align-items: center;

    height: 55px;

    padding-left: 5rem;

    background: var(--color-mantle);

    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.125);
  }

  .heading {
    flex-grow: 0;
    flex-shrink: 0;

    padding: 0;
    margin: 0;
  }

  .main {
    flex: 1;

    display: grid;
    grid-template-rows: 1fr 0.5fr 0.5fr auto;

    overflow: hidden;
  }

  .color-blocks-container {
    line-height: 0;
  }

  .color-block {
    display: inline-block;
    width: 12.5vw;
    height: 12.5vw;
    max-height: 12vh;
  }

  .color-text-blocks-container {
    line-height: 3.125vw;
  }

  .color-text-blocks-container.first {
    align-self: end;
  }

  .color-text-block {
    display: inline-block;
    width: 12.5vw;
    height: 3.125vw;
    max-height: 6vh;

    font-size: 1.5vw;

    text-align: center;
  }

  .color-fields-container {
    padding: 1rem 0;
  }

  .color-field {
    display: inline-block;

    min-width: 12.5vw;
    width: 12.5vw;
    max-width: 12.5vw;

    padding: 0;
    margin: 0;

    border: none;

    overflow: hidden;
  }

  .color-field-legend {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .color-inputs {
    display: grid;

    grid-template-columns: 3fr 1fr;
  }

  .color-inputs input {
    min-width: 0;

    width: 100%;
  }

  @media (max-width: 1200px) {
    .color-fields-container {
      overflow: scroll;
    }

    .color-field {
      display: grid;
      grid-template-columns: 1fr 3fr;

      min-width: 100%;
      width: 100%;
      max-width: 100%;
    }

    .color-field-legend {
      display: contents;

      min-width: 0px;
      width: 100vw;
      max-width: 10px;

      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
    }

    .color-inputs {
      grid-template-columns: auto 4fr;
    }

    .color-inputs input:first-of-type {
      width: 4.5rem;
    }
  }
</style>
