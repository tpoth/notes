# Notes

Dieses Tutorial habe ich auf YT gefunden und nach programmiert.

Der Link dazu: https://www.youtube.com/watch?v=cQ6V7ZHzg8c

Im folgenden protokolliere ich die einzelnen Schritte.

## Einrichten der DEV Umgebung

Terminal starten und die folgenden Befehle absetzen:

```bash
npx create-next-app@latest --ts
```

Möglicherweise bekommt man jetzt die folgende Abfrage:

```bash
Need to install the following packages:
  create-next-app@13.1.6
Ok to proceed? (y)
```

Diese habe ich einfach mit `Enter` bestätigt.

> Da (y) der Default ist muss man das `y` nicht extra mit tippen!

Bei der Frage nach dem Projektnamen

```
? What is your project named? » notes
```

habe ich dann einfach den Projektnamen `notes` verwendet.

Bei der Frage nach eslint

```
√ Would you like to use ESLint with this project? ... No / Yes
```

habe ich einfach mit `Enter` bestätigt.

> Da (Yes) der Default ist muss man hier nichts spezielles auswählen!

Ich verwende - bei der Installation - kein `src` Verzeichnis

```
? Would you like to use `src/` directory with this project? » No / Yes
```

Habe also einfach wieder mit `Enter` bestätigt.

> Da in diesem Fall (No) der Default ist muss man hier mal wieder nichts spezielles auswählen!

Bei der Frage ob ich ein experimentelles `app` Verzeichnis haben möchte

```
? Would you like to use experimental `app/` directory with this project? » No / Yes
```

Habe ebenfalls wieder mit `Enter` bestätigt.

> Da in diesem Fall (No) der Default ist muss man hier nichts anderes auswählen!

Bei der Frage nach dem `import alias` nutze ich auch die Default Vorgaben.

```
? What import alias would you like configured? » @/*
```

> Eine interessante Idee wäre statt dem direkt loslaufenden `npm i` einmal `pnpm` zu verwenden. Da dies doch die performance deutlich erhöht. Anstelle vom hier genutzten `npx` könnte man dann auch `pnpx` nutzen. Hier wäre insbesondere interessant ob das Scaffold Script dann so schlau ist auch `pnpm` für die Installation der Pakete zu nutzen?

Anschließend wechseln wir ins Projektverzeichnis und starten vscode.

```
cd notes
code .
```

##  Installieren von Tailwind CSS

Da wir zum stylen des Frontends tailwind nutzen, muss dies nun zuerst einmal in unser Projekt integriert werden.

Hierzu installieren wir zuerst wie auf der tailwind Website beschrieben die DEV Abhängigkeiten.

```
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

Anschließend muss in der `tailwind.config.js` noch im `content` Array der Pfad zu den `pages` und den `components` eingetragen werden.

Hier meine abschließende `tailwind.config.js`:

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

Damit wir in unserem `next.js` Projekt nun auch die Formatierung mittels tailwind durchführen können **ERSETZEN** wir den gesamten Inhalt der Datei `styles/globals.css`.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

## Starten des lokalen DEV Servers

Nun können wir zum ersten mal unseren Entwicklungswebserver starten. Dies machen wir wie folgt:

```bash
npm run dev
```

Wir können dann einen Browser starten und darin die URL [http://localhost:3000](http://localhost:3000) aufrufen.

Hier sollte die Beispielseite von `next.js` zu sehen sein.

> Diese sieht aufgrund der fehlenden CSS Einträge, die wir während der tailwind Installation gelöscht hatten, nun etwas merkwürdig aus. Das beheben wir aber gleich im nächsten Schritte!

## Bereinigen der alten Styles und testen ob tailwind funktioniert

Die erste Seite die `nex.js` beim starten aufruft wird durch die Datei `pages/index.tsx` repräsentiert.

In dieser Seite sind einige className Attribute notiert, die nun nicht mehr funktionieren können, da wir die darin bezogenen CSS Klassen aus der Datei `styles/globals.css` entfernt haben. Diese entfernen wir komplett.

Weiterhin kommentieren wir den import der css Datei aus, die uns diese Styles in die Komponente (Page) holt, aus.

```tsx
// import styles from '@/styles/Home.module.css'
```

Nun ersetzen wir zu testzwecken die in React üblichen Tags ohne Inhalte

```tsx
<>
  ...
</>
```

durch

```tsx
<div className='mt-10'>
  ...
</div>
```

Wenn tailwind richtig eingebunden ist, sollte jetzt am oberen Rand ein deutlich sichtbarer Abstand zu sehen sein.

## Installation von Prisma

Zunächst installieren wir die DEV Pakete.

```bash
npm install -D prisma
```

Anschließen initialisieren wir Prisma mit dem folgenden Befehl:

```bash
npx prisma init
```

Durch die Initialisierung von Prisma wurden nun zwei neue Dateien erzeugt.

Zum einen wurde das Prisma Schema in der Datei `prisma/schema.prisma` erzeugt.

```prisma
// This is your Prisma schema file,
// learn more about it in the docs: https://pris.ly/d/prisma-schema

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

Zum anderen wurde in der Datei `.env` ein Eintrag für die `DATABASE_URL` erzeugt:

```env
# Environment variables declared in this file are automatically made available to Prisma.
# See the documentation for more detail: https://pris.ly/d/prisma-schema#accessing-environment-variables-from-the-schema

# Prisma supports the native connection string format for PostgreSQL, MySQL, SQLite, SQL Server, MongoDB and CockroachDB.
# See the documentation for all the connection string options: https://pris.ly/d/connection-strings

DATABASE_URL="postgresql://johndoe:randompassword@localhost:5432/mydb?schema=public"
```

Per default wird eine PostgreSQL DB angenommen und in den beiden Konfigurationsdateien beispielhaft Einträge dafür eingerichtet.

> Diese haben natürlich noch keinen Effekt, da die `DATABASE_URL` noch ins Leere zeigt.

Als kurzen aber wichtigen Zwischenschritt, ergänzen wir noch die Datei `.gitignore` um alle Dateien, die mit dem Dateinamen `.env` beginnen. Hierzu muss eine Zeile hinzugefügt werden, die wir am Besten bei `# local env files` einsortieren. Hier ein Auszug aus der aktualisierten Datei:

```gitignore
...
# local env files
.env*
.env*.local
...
```

> Dieser Schritt ist extrem wichtig, da sonst Zugangsdaten im Git Repo landen könnten, was wir in jedem Fall vermeiden sollten.

> Ich werde an dieser Stelle nicht konkret beschreiben wie eine Datenbank und ein passender Zugang erzeugt wird.  Ich gehe davon aus, dass dieses Vorgehen an geeigneter Stelle schon einmal dokumentiert ist!

> Kleiner Hinweis noch für die Nutzung von MySQL Datenbanken:
> Der angelegte Benutzer sollte die globale Rechte (Global Privileges) `ALTER, CREATE, DROP und REFERENCES` erhalten.

Abschließend muss nun in der Datei `.env` ein Eintrag für die `DATABASE_URL` passend zur verwendeten Datenbank erzeugt werden.

> Bitte daran denken, wenn keine Postgresql DB verwendet wird, auch in der `schema.prisma` den DB Provider umzustellen!

## Erzeugen des Models für unsere Anwendung

Hierzu muss in der Datei `prisma/schema.prisma` das Folgende ergänzt werden:

```prisma
model Note {
  id        Int      @id @default(autoincrement())
  title     String
  content   String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

Danach erzeugen wir aus unserm gerade definierten Model `Note` ein entsprechendes DB Schema. mit dem Folgenden Befehl:

```bash
npx prisma db push
```

Jetzt prüfen wir ob die Tabelle für unser Modell erzeugt wurde mittels `Prisma Studio`.

```bash
npx prisma studio
```

Prisma Studio startet automatisch die folgende URL im Default Browser:

[http://localhost:5555](http://localhost:5555)

## Installieren des Prisma Client

```bash
npm install @prisma/client
```

Anschließend können wir den Prisma Client neu generieren lassen.

```bash
npx prisma generate
```

## Erzeugen eines globalen Prisma Clients

Wir erzeugen im root des Projekts ein neues Verzeichnis mit Namen `lib` und darin eine Datei mit dem Namen `prisma.ts` mit dem folgenden Inhalt:

```ts
import { PrismaClient } from '@prisma/client';

declare global {
  var prisma: PrismaClient | undefined;
}

export const prisma = global.prisma || new PrismaClient();

if (process.env.NODE_ENV !== 'production') global.prisma = prisma;
```

Diese Datei kann von überall genutzt werden um einen Prisma Client in unserer App zu nutzen. Dabei wird eine Art Singleton Pattern verwendet, welches verhindern soll, dass mehrere Clients instanziiert werden.

## Aufbauen eine Formulars zum eintragen einer Note

Hierzu schreiben wir die Datei `pages/index.tsx` so um, dass sie den folgenden Inhalt in sich trägt:

```tsx
import type { NextPage } from 'next';
import { useState } from 'react'

interface FormData {
  title: string;
  content: string;
  id: string;
}

export default function Home() {
  const [form, setForm] = useState<FormData>({ title: '', content: '', id: '' })

  return (
    <div>
      <h1 className='text-center font-bold text-2xl mt-4'>Notes</h1>
      <form onSubmit={e => {
        e.preventDefault();
      }} className='w-auto min-w-[25%] max-w-min mx-auto space-y-6 flex flex-col items-stretch'>
        <input
          type="text"
          name=""
          id=""
          placeholder='Title'
          value={form.title}
          onChange={e => {
            setForm({ ...form, title: e.target.value })
          }}
          className='border-2 rounded border-gray600 p-1'
        />
        <textarea
          name=""
          id=""
          placeholder='Content'
          value={form.content}
          onChange={e => {
            setForm({ ...form, content: e.target.value })
          }}
          className='border-2 rounded border-gray600 p-1'
        />
        <button
          type='submit'
          className='bg-blue-500 text-white rounded p-1'>Add +</button>
      </form>
    </div>
  )
}
```

Ein paar Spiegelstriche, was wir in dieser Datei eingebaut haben.

- Formular, bei dem das Standardverhalten abgeschaltet wurde
- FormData als TS interface formuliert um Typsicher zu arbeiten
- Einen State mit FormData erzeugt, der mit leeren Strings initialisiert wurde
- Ein paar Form Felder zum verarbeiten der State Daten des Formulars
- Einen Button, zum abschicken des Formulars
- Etwas Tailwind zum Stylen des Formulars, damit es nicht gar so schlimm aussieht

## Erzeugen eines API Endpunkts zum erzeugen eine neuen Eintrags

Hierzu erzeugen wir die Datei `pages/api/create.ts` mit dem folgenden Inhalt:

```ts
import { prisma } from '@/lib/prisma';
import type { NextApiRequest, NextApiResponse } from 'next'

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  const { title, content } = req.body;

  try {
    await prisma.note.create({
      data: {
        title,
        content
      }
    });
    res.status(200).json({ message: 'Note Created' });
  } catch (error) {
    console.log('Failure');

  }
}
```

## Implementierung des API Aufrufs zum erzeugen eines Note Eintrags

```tsx
import type { NextPage } from 'next';
import { useState } from 'react'

interface FormData {
  title: string;
  content: string;
  id: string;
}

export default function Home() {
  const [form, setForm] = useState<FormData>({ title: '', content: '', id: '' })

  async function create(data: FormData) {
    try {
      fetch('http://localhost:3000/api/create', {
        body: JSON.stringify(data),
        headers: {
          'Content-Type': 'application/json'
        },
        method: 'POST'
      }).then(() => setForm({ title: '', content: '', id: '' }));
    } catch (error) {
      console.log(error);
    }
  }

  const handleSubmit = async (data: FormData) => {
    try {
      create(data);
    } catch (error) {
      console.log(error);
    }
  }

  return (
    <div>
      <h1 className='text-center font-bold text-2xl mt-4'>Notes</h1>
      <form onSubmit={e => {
        e.preventDefault();
        handleSubmit(form);
      }} className='w-auto min-w-[25%] max-w-min mx-auto space-y-6 flex flex-col items-stretch'>
        <input
          type="text"
          placeholder='Title'
          value={form.title}
          onChange={e => setForm({ ...form, title: e.target.value })}
          className='border-2 rounded border-gray600 p-1'
        />
        <textarea
          placeholder='Content'
          value={form.content}
          onChange={e => setForm({ ...form, content: e.target.value })}
          className='border-2 rounded border-gray600 p-1'
        />
        <button
          type='submit'
          className='bg-blue-500 text-white rounded p-1'>Add +</button>
      </form>
    </div>
  )
}
```

## Abfragen der Daten mittels ServerSideProps

```tsx
import { prisma } from '@/lib/prisma';
import type { NextPage } from 'next';
import { GetServerSideProps } from 'next';
import { useRouter } from 'next/router';
import { useState } from 'react'

interface Notes {
  notes: {
    id: string;
    title: string;
    content: string;
  }[]
}

interface FormData {
  title: string;
  content: string;
  id: string;
}

const Home = ({ notes }: Notes) => {
  const [form, setForm] = useState<FormData>({ title: '', content: '', id: '' })
  const router = useRouter();

  const refreshData = () => {
    router.replace(router.asPath)
  }

  async function create(data: FormData) {
    try {
      fetch('http://localhost:3000/api/create', {
        body: JSON.stringify(data),
        headers: {
          'Content-Type': 'application/json'
        },
        method: 'POST'
      }).then(() => {
        setForm({ title: '', content: '', id: '' })
        refreshData();
      });
    } catch (error) {
      console.log(error);
    }
  }

  const handleSubmit = async (data: FormData) => {
    try {
      create(data);
    } catch (error) {
      console.log(error);
    }
  }

  return (
    <div>
      <h1 className='text-center font-bold text-2xl mt-4'>Notes</h1>
      <form onSubmit={e => {
        e.preventDefault();
        handleSubmit(form);
      }} className='w-auto min-w-[25%] max-w-min mx-auto space-y-6 flex flex-col items-stretch'>
        <input
          type="text"
          placeholder='Title'
          value={form.title}
          onChange={e => setForm({ ...form, title: e.target.value })}
          className='border-2 rounded border-gray600 p-1'
        />
        <textarea
          placeholder='Content'
          value={form.content}
          onChange={e => setForm({ ...form, content: e.target.value })}
          className='border-2 rounded border-gray600 p-1'
        />
        <button
          type='submit'
          className='bg-blue-500 text-white rounded p-1'>Add +</button>
      </form>
      <div className='w-auto min-w-[25%] max-w-min mt-20 mx-auto space-y-6 flex flex-col  items-stretch'>
        <ul>
          {notes.map(note => (
            <li key={note.id} className='border-b border-gray-600 p-2'>
              <div className='flex justify-between'>
                <div className='flex-1'>
                  <h3 className='font-bold'>{note.title}</h3>
                  <p className='text-sm'>{note.content}</p>
                </div>
              </div></li>
          ))}
        </ul>
      </div>
    </div>
  )
}

export default Home;

export const getServerSideProps: GetServerSideProps = async () => {
  const notes = await prisma.note.findMany({
    select: {
      title: true,
      content: true,
      id: true,
    }
  })

  return {
    props: {
      notes
    }
  }
}
```

## API zum löschen einer Notiz

Wir legen eine neue Datei mit dem folgenden Inhalt in `pages/api/note/[id].ts` an:

```ts
import { prisma } from '@/lib/prisma';
import type { NextApiRequest, NextApiResponse } from 'next'

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  const { title, content } = req.body;

  try {
    await prisma.note.create({
      data: {
        title,
        content
      }
    });
    res.status(200).json({ message: 'Note Created' });
  } catch (error) {
    console.log('Failure');

  }
}
```

## Umsetzung zum löschen einer Notiz im Frontend

```tsx
import { prisma } from '@/lib/prisma';
import type { NextPage } from 'next';
import { GetServerSideProps } from 'next';
import { useRouter } from 'next/router';
import { useState } from 'react'

interface Notes {
  notes: {
    id: string;
    title: string;
    content: string;
  }[]
}

interface FormData {
  title: string;
  content: string;
  id: string;
}

const Home = ({ notes }: Notes) => {
  const [form, setForm] = useState<FormData>({ title: '', content: '', id: '' })
  const router = useRouter();

  const refreshData = () => {
    router.replace(router.asPath)
  }

  async function create(data: FormData) {
    try {
      fetch('http://localhost:3000/api/create', {
        body: JSON.stringify(data),
        headers: {
          'Content-Type': 'application/json'
        },
        method: 'POST'
      }).then(() => {
        setForm({ title: '', content: '', id: '' })
        refreshData();
      });
    } catch (error) {
      console.log(error);
    }
  }

  async function deleteNote(id: string) {
    try {
      fetch(`http://localhost:3000/api/note/${id}`, {
        headers: {
          'Content-Type': 'application/json'
        },
        method: 'DELETE'
      }).then(() => {
        refreshData();
      })
    } catch (error) {
      console.log(error);
    }
  }

  const handleSubmit = async (data: FormData) => {
    try {
      create(data);
    } catch (error) {
      console.log(error);
    }
  }

  return (
    <div>
      <h1 className='text-center font-bold text-2xl mt-4'>Notes</h1>
      <form onSubmit={e => {
        e.preventDefault();
        handleSubmit(form);
      }} className='w-auto min-w-[25%] max-w-min mx-auto space-y-6 flex flex-col items-stretch'>
        <input
          type="text"
          placeholder='Title'
          value={form.title}
          onChange={e => setForm({ ...form, title: e.target.value })}
          className='border-2 rounded border-gray600 p-1'
        />
        <textarea
          placeholder='Content'
          value={form.content}
          onChange={e => setForm({ ...form, content: e.target.value })}
          className='border-2 rounded border-gray600 p-1'
        />
        <button
          type='submit'
          className='bg-blue-500 text-white rounded p-1'>Add +</button>
      </form>
      <div className='w-auto min-w-[25%] max-w-min mt-20 mx-auto space-y-6 flex flex-col  items-stretch'>
        <ul>
          {notes.map(note => (
            <li key={note.id} className='border-b border-gray-600 p-2'>
              <div className='flex justify-between'>
                <div className='flex-1'>
                  <h3 className='font-bold'>{note.title}</h3>
                  <p className='text-sm'>{note.content}</p>
                </div>
                <button onClick={() => deleteNote(note.id)} className='bg-red-500 px-3 text-white rounded'>X</button>
              </div>
            </li>
          ))}
        </ul>
      </div>
    </div>
  )
}

export default Home;

export const getServerSideProps: GetServerSideProps = async () => {
  const notes = await prisma.note.findMany({
    select: {
      title: true,
      content: true,
      id: true,
    }
  })

  return {
    props: {
      notes
    }
  }
}
```

## Ändern eines Notiz Eintrags

```tsx
import { prisma } from '@/lib/prisma';
import type { NextPage } from 'next';
import { GetServerSideProps } from 'next';
import { useRouter } from 'next/router';
import { useState } from 'react'

interface Notes {
  notes: {
    id: string;
    title: string;
    content: string;
  }[]
}

interface FormData {
  title: string;
  content: string;
  id: string;
}

const Home = ({ notes }: Notes) => {
  const [form, setForm] = useState<FormData>({ title: '', content: '', id: '' })
  const router = useRouter();

  const refreshData = () => {
    router.replace(router.asPath)
  }

  async function create(data: FormData) {
    try {
      fetch('http://localhost:3000/api/create', {
        body: JSON.stringify(data),
        headers: {
          'Content-Type': 'application/json'
        },
        method: 'POST'
      }).then(() => {
        if (data.id) {
          deleteNote(data.id);
          setForm({ title: '', content: '', id: '' })
          refreshData();
        } else {
          setForm({ title: '', content: '', id: '' })
          refreshData();
        }
      });
    } catch (error) {
      console.log(error);
    }
  }

  async function deleteNote(id: string) {
    try {
      fetch(`http://localhost:3000/api/note/${id}`, {
        headers: {
          'Content-Type': 'application/json'
        },
        method: 'DELETE'
      }).then(() => {
        refreshData();
      })
    } catch (error) {
      console.log(error);
    }
  }

  const handleSubmit = async (data: FormData) => {
    try {
      create(data);
    } catch (error) {
      console.log(error);
    }
  }

  return (
    <div>
      <h1 className='text-center font-bold text-2xl mt-4'>Notes</h1>
      <form onSubmit={e => {
        e.preventDefault();
        handleSubmit(form);
      }} className='w-auto min-w-[25%] max-w-min mx-auto space-y-6 flex flex-col items-stretch'>
        <input
          type="text"
          placeholder='Title'
          value={form.title}
          onChange={e => setForm({ ...form, title: e.target.value })}
          className='border-2 rounded border-gray600 p-1'
        />
        <textarea
          placeholder='Content'
          value={form.content}
          onChange={e => setForm({ ...form, content: e.target.value })}
          className='border-2 rounded border-gray600 p-1'
        />
        <button
          type='submit'
          className='bg-blue-500 text-white rounded p-1'>Add +</button>
      </form>
      <div className='w-auto min-w-[25%] max-w-min mt-20 mx-auto space-y-6 flex flex-col  items-stretch'>
        <ul>
          {notes.map(note => (
            <li key={note.id} className='border-b border-gray-600 p-2'>
              <div className='flex justify-between'>
                <div className='flex-1'>
                  <h3 className='font-bold'>{note.title}</h3>
                  <p className='text-sm'>{note.content}</p>
                </div>
                <button onClick={() => setForm({ title: note.title, content: note.content, id: note.id })} className='bg-blue-500 px-3 text-white rounded'>Update</button>
                <button onClick={() => deleteNote(note.id)} className='bg-red-500 px-3 text-white rounded'>X</button>
              </div>
            </li>
          ))}
        </ul>
      </div>
    </div>
  )
}

export default Home;

export const getServerSideProps: GetServerSideProps = async () => {
  const notes = await prisma.note.findMany({
    select: {
      title: true,
      content: true,
      id: true,
    }
  })

  return {
    props: {
      notes
    }
  }
}
```