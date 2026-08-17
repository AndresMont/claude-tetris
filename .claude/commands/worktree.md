---
description: Crea un git worktree aislado y ejecuta ahí el requerimiento dado
argument-hint: <descripción del requerimiento>
---

Requerimiento: $ARGUMENTS

Pasos a ejecutar, en orden:

1. Deriva un nombre corto en kebab-case (2-4 palabras, sin acentos, solo `[a-z0-9-]`) que resuma el requerimiento. Este será `<nombre>`.
2. Crea el worktree con nueva branch propia, sin tocar el árbol principal:
   ```
   git worktree add .trees/<nombre> -b <nombre>
   ```
   Si el directorio `.trees/<nombre>` o la branch `<nombre>` ya existen, ajusta el nombre (sufijo `-2`, `-3`, ...) hasta que sea único.
3. Ejecuta TODO el trabajo del requerimiento dentro de `.trees/<nombre>` (rutas de archivo bajo esa carpeta), nunca en el working directory principal. No mezcles cambios con el árbol principal ni con otros worktrees existentes en `.trees/`.
4. Al terminar, reporta: nombre del worktree, branch creada, y resumen breve de los cambios hechos ahí. No hagas commit/push/merge salvo que el requerimiento lo pida explícitamente.
