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