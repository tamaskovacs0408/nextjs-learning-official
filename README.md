# Next.js App Router Course - Final

## Struktúra

- `/app`: Tartalmazza az összes route-ot, komponenst és logikát az alkalmazáshoz
- `/app/lib`: Tartalmazza az alkalmazásban használt függvényeket, például újrafelhasználható utility függvényeket és data fetching függvényeket
- `/app/ui`: Tartalmazza az alkalmazás összes UI komponensét, például cards, táblázatokat és forms
- `/public`: Tartalmazza az alkalmazás statikus eszközeit, például a képeket
- `/scripts`: Tartalmaz egy fájlt, amelyet az adatbázis feltöltéséhez használsz

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
  weight: ["400", "700"],
});
```

Ezután az adott fájlba importálja (`page`, `layout`, komponens stb.) hozzá tudjuk adni az adott elem `className`-éhez az alábbi szerint:
`<body className={`${inter.className} antialiased`}>{children}</body>`.
(Itt a Tailwind `antialased` osztályt is hozzáadod, amely kisimítja a betűtípust. Nem szükséges használni ezt az osztályt, de szépen kiemeli a betűtípusokat.)

### Images

A Next.js képes statikus eszközök, például képek kiszolgálására a legfelső szintű `/public` mappa alatt. A `/public` mappán belüli fájlokra az alkalmazásban lehet hivatkozni.

A `next/image` kompnens használatával automatikusan optimalizálhatjuk a képeket.

#### Image component

A `<Image>` komponens a HTML `<img>` tag kiterjesztése, és automatikus képoptimalizálással rendelkezik, például:

- A képek betöltésekor automatikusan megakadályozza az elrendezés eltolódását.
- A képek átméretezése a nagyméretű képek kisebb viewport-al rendelkező eszközökre.
- A képek alapértelmezett `lazy loading`-ja (a képek akkor töltődnek be, amikor a viewport-ba kerülnek).
- Képek kiszolgálása modern formátumokban, például `WebP` és `AVIF` formátumban, ha a böngésző támogatja.

Érdemes a képek szélességét és magasságát beállítani, hogy elkerüljük az elrendezés eltolódását, ezeknek a képarányoknak azonosnak kell lenniük a forrásképpel.
Észre fogod venni a `hidden` osztályt is, hogy eltávolítsd a képet a DOM-ból a mobil képernyőkön, és az `md:block` osztályt, hogy megjelenítsd a képet az asztali képernyőkön(Tailwind).

## Routes

Úgy tudunk új route-ot létrehozni, hogy az `app` mappán belül létrehozunk egy új mappát a kívánt route nevével, aminek tartalmaznia kell egy `pages.tsx` fájlt (pl. `/app/dasboard/pages.tsx`). Ez után már elérhető lesz a megadott (dashboard) oldal az urlben (`www.mypage.com/dashboard`). Az `app` könyvtárba más mappákat is elhelyezhetünk (`lib`, `ui` stb.), mivel a Next.js csak azt fogja route-nak venni, ahol van `pages.tsx` fájl.

### Nested routing

A Next.js file-system routinget használ, ahol a mappákat egymásba ágyazott route-ok létrehozására használják. Minden mappa egy routeszegmenst képvisel, amely egy URL-szegmenshez kapcsolódik.

A `page.tsx` egy speciális Next.js fájl, amely egy React komponenst exportál, amely az route felhasználói felületét tartalmazza. Az alkalmazásodban már van egy page fájl: `/app/page.tsx` - ez a kezdőlap, amely a `/` routehoz kapcsolódik.

Nested route-ok létrehozásához egymásba ágyazott mappákat készíthetsz, saját `page.tsx` fájlokkal.

## Layout

A Next.js-ben egy speciális `layout.tsx` fájlt használhatsz a több oldal között megosztott felhasználói felület létrehozásához.

A `Layout` komponens megkapja a Children propot. A `children` lehet egy oldal vagy egy másik layout. Ebben az esetében a `/dashboardon` belüli oldalak automatikusan egy Layouton belül lesznek beágyazva.

```tsx
import SideNav from "@/app/ui/dashboard/sidenav";

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
      <div className="w-full flex-none md:w-64">
        <SideNav />
      </div>
      <div className="flex-grow p-6 md:overflow-y-auto md:p-12">{children}</div>
    </div>
  );
}
```

A layout használatának egyik előnye, hogy a navigáció során csak az oldalkomponensek frissülnek, míg a layout nem kerül újrarendezésre. A Next.js-ben ezt _részleges renderelésnek_ (`partial rendering`) nevezik.

### Root layout

Az `/app` mappában található `layout.tsx`-et nevezzük **root layout**-nak. Ez egy kötelező elem! A root layout-hoz hozzáadott UI az alkalmazás minden oldalán közös lesz. A root layoutot használhatja a `<html>` és `<body>` címkék módosítására, valamint metaadatok hozzáadására.

## Navigating between pages

### <Link> component

A Next.js-ben a `Link` komponens segítségével az alkalmazás oldalai között linkeket hozhat létre. A `<Link>` lehetővé teszi a kliensoldali navigációt JavaScript segítségével. Bár az alkalmazás egyes részei a szerveren kerülnek renderelésre, a navigáció gyorsabb, és nincs teljes oldalfrissítés - így sokkal inkább webes alkalmazásnak tűnik.

A `<Link>` komponens a `<a>`-hoz hasonlóan egy `href`-be várja az route-ot.

`<Link href="/dashboard">Dashboard</Link>`

### Aktív oldal

Egy gyakori felhasználói felület mintája, hogy egy aktív linket jelenít meg, amely jelzi a felhasználónak, hogy éppen melyik oldalon tartózkodik. Ehhez az URL-ből meg kell szereznie a felhasználó aktuális elérési útvonalát. A Next.js biztosít egy `usePathName()` nevű hook-ot, amelyet a route ellenőrzésére használhatunk.

Mivel a `usePathName()` egy hook, a `nav-links.tsx`-et egy klienskomponenssé kell alakítanod. Adja hozzá a React `"use client"` direktíváját a fájl tetejéhez, majd importálja a `usePathName()` funkciót a `next/navigation`-ból, majd rendeljük hozzá egy változóhoz (`pathname`).

`const pathname = usePathName();`

Ez után a már elmlített `clsx` könyvtárral tudunk conditional style-t beállítani (Tailwind) az aktív linkre.

```tsx
<Link
  key={link.name}
  href={link.href}
  className={clsx(
    "flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3",
    {
      "bg-sky-100 text-blue-600": pathname === link.href,
    }
  )}
>
  <LinkIcon className="w-6" />
  <p className="hidden md:block">{link.name}</p>
</Link>
```
A kliensoldali navigáció mellett a Next.js automatikusan kóddal osztja fel az alkalmazást routeszegmensek szerint. Ez eltér a hagyományos SPA-tól, ahol a böngésző az alkalmazás teljes kódját betölti a kezdeti betöltéskor.

A kód route-ok szerinti felosztása azt jelenti, hogy az oldalak elszigeteltek lesznek. Ha egy bizonyos oldal hibát dob, az alkalmazás többi része továbbra is működik.

Továbbá, a production-ben, amikor `<Link>` komponensek jelennek meg a böngésző nézetablakában, a Next.js automatikusan előhívja a háttérben a linkelt route kódját. Mire a felhasználó rákattint a linkre, a céloldal kódja már betöltődik a háttérben, és az oldalváltás szinte azonnal megtörténik!