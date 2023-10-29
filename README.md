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

## Create Database

A Vercel Dashboard *Storage* tab-on hozzunk létre egy `Postgres` adatbázist. Adjunk nevet és válasszuk ki a régiót (itt előszőr a project settings/functions rész alatt állítsunk be európai (Párizs/Frankfurt) régiót, hogy az adatbázisnál is ezt tudjuk választani).

Ha létrehoztuk a Postgres adatbázist, az `.env.local` tabon látható adatokat másoljuk a projectünk `.env` fájljába (ha nincs ilyen, hozzunk létre). **NAGYON FONTOS, HOGY A .gitingore FÁJLBAN MEG LEGYEN ADVA A .env FÁJLOK KISZŰRÉSE!!!**

Végül futtassuk az `npm i @vercel/postgres` parancsot a terminálban a Vercel Postgres SDK telepítéséhez.

### Seed database

Most, hogy létrehoztuk az adatbázist, töltsük meg néhány kezdeti adattal. Ez lehetővé teszi, hogy legyen néhány adatunk, amivel dolgozhatunk a dashboard létrehozásakor.

A `/scripts` mappában van egy `seed.js` nevű fájl. Ez a script tartalmazza a invoices, customers, users és revenue táblák létrehozására és seedingre vonatkozó utasításokat.

A script SQL-t használ a táblák létrehozásához, és a `placeholder-data.js` fájlból származó adatokkal tölti fel őket, miután létrehozta őket.

Ezután a `package.json` fájlban a következő sorral egészítse ki a `scripts` részt:

```js
"scripts": {
  "build": "next build",
  "dev": "next dev",
  "start": "next start",
  "seed": "node -r dotenv/config ./scripts/seed.js"
},
```

Ez a parancs fogja végrehajtani a `seed.js`-t. Mielőtt futtathatnánk a parancsot, telepítsük a *bcrypt* könyvtárat (`npm i bcrypt`), amely a felhasználói jelszavak hashelésére szolgál.

Most futtassuk az `npm run seed`-et. Látnod kell néhány `console.log` üzenetet a terminálodban, hogy tudd, a script fut.

```js
Created "users" table
Seeded 1 users
Created "customers" table
Seeded 10 customers
Created "invoices" table
Seeded 15 invoices
Created "revenue" table
Seeded 12 revenue
```

Ezek után a vercel oldalán lévő Storage tabomn a Data fülre kattintva már kiválaszthatjuk és láthatjuk az előbb betöltött adatokat az adatbázisból

### Query-k futtatása

A *query* fülre váltással interakcióba léphetünk az adatbázisával. Ez a rész támogatja a szabványos SQL parancsokat. Például a `DROP TABLE customers` beírása törli a `"customers"` táblát az összes adatával együtt - **ezért légy óvatos**!

Futtassuk az első adatbázis-lekérdezését: Illesszük be és futtassa az alábbi SQL kódot a Vercel query felületén:

```sql
SELECT invoices.amount, customers.name
FROM invoices
JOIN customers ON invoices.customer_id = customers.id
WHERE invoices.amount = 666;
```

## Fetching data

### API Layer

Az API-k egy közvetítő layert jelentenek az alkalmazáskód és az adatbázis között. Van néhány olyan eset, amikor API-t használhat:

- Ha olyan 3rd party szolgáltatásokat használ, amelyek API-t biztosítanak.
- Ha adatokat hív le a kliens oldalról, akkor olyan API-layert szeretne, amely a kiszolgálón fut, hogy elkerülje az adatbázis titkainak felfedését az kliens számára.
A Next.js-ben API végpontokat hozhat létre Route Handlerek segítségével.

### Database queries

Amikor egy fullstack alkalmazást készítünk, logikát is kell írni az adatbázissal való interakcióhoz. Az olyan relációs adatbázisok esetében, mint a `Postgres`, ezt megtehetjük `SQL` vagy egy `ORM`, mint a `Prisma` segítségével.

Van néhány olyan eset, amikor adatbázis-lekérdezéseket kell írni:

- Az API végpontok létrehozásakor logikát kell írni az adatbázissal való interakcióhoz.
- Ha `React Server Components`-t használsz (adatok lekérése a szerveren), akkor kihagyhatod az API réteget, és közvetlenül lekérdezheted az adatbázisodat anélkül, hogy kockáztatnád az adatbázisod titkainak felfedését az ügyfél előtt.

### Using Server Components to fetch data

Alapértelmezés szerint a Next.js alkalmazások `React Server Components`-t használnak, és szükség esetén választhatjuk a `Client Components`-t(`'use client`). Az adatok `React Server Components` segítségével történő lekérdezésének van néhány előnye:

- A `React Server Components` a serveren hajtódnak végre, így a drága adatlehívásokat és logikát a serveren tarthatja, és csak az eredményt küldheti el az ügyfélnek.
- A `React Server Components` támogatják az `promise`-okat, így egyszerűbb megoldást nyújtanak az olyan `async` feladatokhoz, mint az `data fetching`. Használhatja az `async/await` szintaxist anélkül, hogy `useEffect`, `useState` vagy 3rd party data fetching library-hoz kellene nyúlnia.
Mivel a `React Server Components` a kiszolgálón hajtódnak végre, közvetlenül lekérdezheti az adatbázist további API-réteg nélkül.

### Using SQL

Ehhez a projecthez adatbázis query-ket fogunk írni az adatlekérésekhez. 

Van néhány ok, amiért SQL-t fogunk használni:

- Az SQL a relációs adatbázisok lekérdezésének ipari szabványa (pl. az ORM-ek SQL-t generálnak a under the hood).
- Az SQL alapszintű ismerete segíthet megérteni a relációs adatbázisok alapjait.
- Az SQL sokoldalú, lehetővé teszi, hogy konkrét adatokat kérjen le és manipuláljon.
- A Vercel Postgress SDK használata védelmet nyújt az `SQL-injection` ellen.

A `sql` függvénnyel tudunk lekérdezéseket végrehajtani az adatbázison (`import { sql } from '@vercel/postgres';`). Az `sql` függvényt bármelyik `Server Component`-ben meg lehet hívni. A könnyebben a komponensekben való könnyebb navigálás érdekében, az összes adatlekérdezést a `data.ts` fájlban történik, innen importálhatjuk őket a komponensekbe.


Figyeljünk oda arra:

- Az adatkérések véletlenül blokkolják egymást, és így egy `request waterfall` jön létre.
- Alapértelmezés szerint a Next.js a teljesítmény javítása érdekében előrendezi (prerenders) az útvonalakat, ezt nevezzük statikus renderelésnek(Static Rendering). Tehát ha az adatok megváltoznak, az nem fog megjelenni a dashboardon.

#### Request waterfall

A "waterfall" a hálózati kérések olyan sorozatára utal, amely az előző kérések teljesítésétől függ. Az adatlekérdezés esetében minden egyes kérés csak akkor kezdődhet el, ha az előző kérés visszaadta az adatokat. Például meg kell várnunk a `fetchRevenue()` végrehajtását, mielőtt a `fetchLatestInvoices()` elindulhat, és így tovább.

```tsx
const revenue = await fetchRevenue();
const latestInvoices = await fetchLatestInvoices(); // wait for fetchRevenue() to finish
const {
  numberOfInvoices,
  numberOfCustomers,
  totalPaidInvoices,
  totalPendingInvoices,
} = await fetchCardData(); // wait for fetchLatestInvoices() to finish
```

Ez a minta nem feltétlenül rossz. Előfordulhatnak olyan esetek, amikor `watrerfall`-t akarsz, mert azt szeretnéd, hogy egy feltétel teljesüljön a következő kérés előtt. Például először a felhasználó azonosítóját és profilinformációit szeretné lekérdezni. Ha már megvan az azonosító, akkor a barátok listáját is lekérdezhetjük. Ebben az esetben minden egyes kérés az előző kérésből visszakapott adatoktól függ.

Ez a viselkedés azonban nem szándékos is lehet, és hatással lehet a teljesítményre.

#### Parallel data fetching

A `waterfall` elkerülésének gyakori módja, hogy az összes adatkérést egyszerre - párhuzamosan - kezdeményezzük.

JavaScriptben a `Promise.all()` vagy `Promise.allSettled()` függvények segítségével az összes promiset egyszerre kezdeményezheti. 
Például a `data.ts`-ben a `Promise.all()` függvényt használjuk a `fetchCardData()` függvényben.

```ts
export async function fetchCardData() {
  try {
    const invoiceCountPromise = sql`SELECT COUNT(*) FROM invoices`;
    const customerCountPromise = sql`SELECT COUNT(*) FROM customers`;
    const invoiceStatusPromise = sql`SELECT
         SUM(CASE WHEN status = 'paid' THEN amount ELSE 0 END) AS "paid",
         SUM(CASE WHEN status = 'pending' THEN amount ELSE 0 END) AS "pending"
         FROM invoices`;
 
    const data = await Promise.all([
      invoiceCountPromise,
      customerCountPromise,
      invoiceStatusPromise,
    ]);
    // ...
  }
}
```

**JÓ TUDNI**

A `Promise.allSettled()` függvénnyel a `status`- és `value` key-t tartalmazó objektumok tömbjét is visszaadhatja, így ellenőrizheti, hogy egy promise státusza teljesült vagy elutasított, mielőtt az értéket átadja a komponensnek. Ez akkor hasznos, ha a hibákat kecsesebben akarja kezelni.

Ezt a mintát használva:

- Az összes adatlekérdezés végrehajtását egyszerre kezdheti el, ami teljesítménynövekedést eredményezhet.
- Használjon egy natív JavaScript mintát, amely bármilyen könyvtárra vagy keretrendszerre alkalmazható.
Azonban... van egy hátránya ennek a JavaScript-mintának: mi történik, ha egy adatkérés lassabb, mint az összes többi?

## Dynamic Rendering