# Next.js App Router Course - Final

## Struktúra

-`/app`: Tartalmazza az összes route-ot, komponenst és logikát az alkalmazáshoz
-`/app/lib`: Tartalmazza az alkalmazásban használt függvényeket, például újrafelhasználható utility függvényeket és data fetching függvényeket
-`/app/ui`: Tartalmazza az alkalmazás összes UI komponensét, például cards, táblázatokat és forms
-`/public`: Tartalmazza az alkalmazás statikus eszközeit, például a képeket
-`/scripts`: Tartalmaz egy fájlt, amelyet az adatbázis feltöltéséhez használsz

## Styling

Az `/app/ui` mappában látni fogsz egy `global.css` nevű fájlt. Ezt a fájlt arra használhatja, hogy CSS szabályokat adjon hozzá az alkalmazás összes route-hoz - például CSS reset szabályokat, az egész webhelyre kiterjedő stílusokat a HTML-elemekhez, például a linkekhez, és így tovább.

A `global.css` állományt az alkalmazás bármelyik komponensébe importálhatja, de általában jó gyakorlat, ha a legfelső szintű komponenshez adja hozzá. A Next.js-ben ez a root layout..

A globális stílusok hozzáadása az alkalmazáshoz a `/app/layout.tsx` fájlba történő navigálással és a `global.css` fájl importálásával történik.

### clsx

A `clsx` library arra való, hogy conditional styles-t tudjunk írni (pl tailwindcss-el)

[documentation](https://github.com/lukeed/clsx)

```tsx
import clsx from 'clsx';
 
export default function InvoiceStatus({ status }: { status: string }) {
  return (
    <span
      className={clsx(
        'inline-flex items-center rounded-full px-2 py-1 text-sm',
        {
          'bg-gray-100 text-gray-500': status === 'pending',
          'bg-green-500 text-white': status === 'paid',
        },
      )}
    >
    // ...
)}
```

## Optimizing fonts & images

### Fonts

A Cumulative Layout Shift egy olyan mérőszám, amelyet a Google egy weboldal teljesítményének és felhasználói élményének értékelésére használ. A betűtípusok esetében az elrendezéseltolódás akkor következik be, amikor a böngésző a szöveget kezdetben egy tartalék- vagy rendszerbetűtípussal jeleníti meg, majd a betöltés után egy egyéni betűtípusra cseréli azt. Ez a csere a szöveg méretének, távolságának vagy elrendezésének megváltozását okozhatja, ami az elemek eltolódását eredményezi.

A `next.js` automatikusan optimalizálja a betűtípusokat az alkalmazásban, ha a `next/font` modult használja. Ezt úgy teszi, hogy a betűtípusfájlokat a build-kor letölti, és a többi statikus eszközzel együtt tárolja őket. Ez azt jelenti, hogy amikor egy felhasználó meglátogatja az alkalmazást, nem történik további hálózati kérés a betűtípusok iránt, ami hatással lenne a teljesítményre.

A `ui` könyvtárban létrehozott `fonts` fájlba importáljuk a kívánt betűtípusokat a `next/font/google` könyvtárból. Ezután hozzunk létre és exportáljuk ki változóba az adott betűtípust, megadva neki a `subset` alatt néhány alapbeállítást.

```ts
import { Inter, Lusitana } from "next/font/google";

export const inter = Inter({ subsets: ["latin"] });
export const lusitana = Lusitana({
  subsets: ["latin"],
  weight: ["400", "700"]
});
```

Ezután az adott fájlba importálja (`page`, `layout`, komponens stb.) hozzá tudjuk adni az adott elem `className`-éhez az alábbi szerint: 
`<body className={`${inter.className} antialiased`}>{children}</body>`.
(Itt a Tailwind `antialased` osztályt is hozzáadod, amely kisimítja a betűtípust. Nem szükséges használni ezt az osztályt, de szépen kiemeli a betűtípusokat.)