# Example Project Initialization

Everything setup to code along with Huntabyte on his [`Full-Stack CRUD Application with SvelteKit & Prisma`](https://youtu.be/E9J2VXd-bzE) YouTube tutorial.

The original Github repo can be found [`here`](https://github.com/huntabyte/sk-prisma)

Resources:
- [`SvelteKit`](https://kit.svelte.dev/)
- [`Prisma`](https://www.prisma.io/)
- [`Pico.css`](https://picocss.com/)

Recommended Plugin:
- [`SQLite Viewer`](https://marketplace.visualstudio.com/items?itemName=qwtel.sqlite-viewer)

install:
npm i @prisma/client
npm i prisma -D

initialize:
npx prisma init

update:
datasource db {
    provider = "sqlite"
    url = "file:./dev.sqlite"
}

add (schema.prisma):
model Article {
    id Int @id @default(autoincrement())
    title String
    content String
}

run:
npx prisma db push

create:
src/lib/server/prisma.ts

add (app.d.ts):
import type { PrismaClient } from "@prisma/client";

declare global {
    namespace App {
		// interface Error {}
		// interface Locals {}
		// interface PageData {}
		// interface Platform {}
	}
    var prisma: PrismaClient
}
export {};

add (prisma.ts):
import { PrismaClient } from "@prisma/client";

const prisma = global.prisma || new PrismaClient()

if (process.env.NODE_ENV === "development") {
    global.prisma = prisma
}

export { prisma }

create:
routes/+page.server.ts

add (+page.server.ts):

import type { Actions } from "./$types";

export const actions:  Actions = {
    createArticle: async ({ request }) => {
        const { title, content } = Object.fromEntries(await request.formData()) as {
            title: string
            content: string
        }

        try {
            await prisma.title.create({
                data: {
                    title,
                    content
                }
            })
        } catch (err) {
            console.error(err);
            return fail(500, { message: "Could not create the article." });
        }

        return {
            status: 201
        }
    }
}

