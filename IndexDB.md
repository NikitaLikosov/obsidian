Надо переписать на функциональный стиль когда то!
Просто храни состояние в переменной а не в классе!!!

```js
import * as Y from 'yjs'
import debounce from 'utils/debounce'

const DBNAME = 'yjs'
const TABLENAME = 'docs'

const TIME_DEBOUNCE_UPDATE = 1000

class IndexedDBStore {
  static async makeDocStorage(id: string) {
    const db = await IndexedDBStore.initializeDB()
    if (db instanceof IDBDatabase) {
      return new IndexedDBStore(id, db)
    }
  }

  static async initializeDB() {
    return new Promise((resolve, reject) => {
      const request = indexedDB.open(DBNAME, 1)
      request.onerror = reject
      request.onupgradeneeded = () => {
        if (!request.result.objectStoreNames.contains(TABLENAME))
          request.result.createObjectStore(TABLENAME, { keyPath: 'id' })

        resolve(request.result)
      }

      request.onsuccess = () => {}
    })
  }

  constructor(
    private id: string,
    private db: IDBDatabase
  ) {
    this.setReloadConnections()
  }

  setReloadConnections() {
    const reloadDB = async () => {
      const newDb = await IndexedDBStore.initializeDB()
      if (newDb instanceof IDBDatabase) {
        this.db = newDb
      }
    }

    this.db.onclose = reloadDB
    this.db.onabort = reloadDB
  }

  updateDoc: (doc: Y.Doc) => unknown = debounce((doc: Y.Doc) => {
    return new Promise((resolve, reject) => {
      const buff = Y.encodeStateAsUpdate(doc)
      const transaction = this.db.transaction([TABLENAME], 'readwrite')
      const objectStore = transaction.objectStore(TABLENAME)

      const requestWrite = objectStore.put({ id: this.id, data: buff })

      requestWrite.onsuccess = request => {
        if (request.target)
          resolve((request!.target as unknown as { result: string }).result as string)
      }
      requestWrite.onerror = reject
    })
  }, TIME_DEBOUNCE_UPDATE).fn

  getDoc(): Promise<Uint8Array> {
    return new Promise((resolve, reject) => {
      const transaction = this.db.transaction([TABLENAME], 'readonly')
      const objectStore = transaction.objectStore(TABLENAME)

      const requestRead = objectStore.get(this.id)

      requestRead.onsuccess = event => {
        const result = (event.target as IDBRequest).result
        if (result) {
          const buffer = result.data as Uint8Array
          resolve(buffer)
        } else {
          resolve(new Uint8Array(0))
        }
      }

      requestRead.onerror = reject
    })
  }
}

export default IndexedDBStore
```

[[Работа]]