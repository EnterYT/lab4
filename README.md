# lab4
## MyHashTable Class 
* ```java
public class MyHashTable<K, V>{

    private class HashNode<K, V> {
        private K key;
        private V value;
        private HashNode<K, V> next;

        public HashNode(K key, V value) {
            this.key = key;
            this.value = value;
        }

        @Override
        public String toString() {
            return "{" + key + " " + value + "}";
        }
    }

    private HashNode<K, V>[] chainArray;
    private int M = 11;
    private int size;

    public MyHashTable() {
        this.chainArray = new HashNode[M];
    }

    public MyHashTable(int M) {
        this.M = M;
        this.chainArray = new HashNode[M];
    }

    private int hash(K key) {
        int hashCode = key.hashCode();
        int index = Math.abs(hashCode) % M;
        return index;
    }

    private void resize(){
        int prevM = M;
        M = M * 2;
        size = 0;
        HashNode<K, V>[] prevChainArray = chainArray;
        chainArray = new HashNode[M];
        for (int i = 0; i < prevM; i++) {
            HashNode<K, V> node = prevChainArray[i];
            while (node != null) {
                HashNode<K, V> next = node.next;
                node.next = null;
                put(node.key, node.value);
                node = next;
            }
        }
    }

    public void put(K key, V value) throws IllegalArgumentException, KeyAlreadyExistsException {
        if (key == null || value == null) {
            throw new IllegalArgumentException("Key or value cannot be null.");
        }
        if (M * 4 < size) {
            resize();
        }
        int index = hash(key);
        HashNode<K, V> node = chainArray[index];
        HashNode<K, V> prev = null;
        if (node == null){
            chainArray[index] = new HashNode<>(key, value);
        } else {
            while (node != null) {
                if (node.key.equals(key)) {
                    node.value = value;
                    return;
                }
                prev = node;
                node = node.next;
            }
            prev.next = new HashNode<>(key, value);
        }
        size++;
    }

    public V get(K key) {
        int index = hash(key);
        HashNode<K, V> node = chainArray[index];
        while (node != null) {
            if (node.key.equals(key)) {
                return node.value;
            }
            node = node.next;
        }
        return null;
    }

    public V remove(K key) {
        int index = hash(key);
        HashNode<K, V> node = chainArray[index];
        HashNode<K, V> prev = null;
        while (node != null) {
            if (key.equals(node.key)) {
                if (prev == null) {
                    chainArray[index] = node.next;
                } else {
                    prev.next = node.next;
                }
                size--;
                return node.value;
            }
            prev = node;
            node = node.next;
        }
        return null;
    }

    public boolean contains(V value) throws NullPointerException {
        if (value == null) {
            throw new NullPointerException("Value cannot be null.");
        }
        for (int i = 0; i < M; i++) {
            HashNode<K, V> node = chainArray[i];
            while (node != null) {
                if (node.value.equals(value)) {
                    return true;
                }
                node = node.next;
            }
        }
        return false;
    }

    public K getKey(V value) {
        for (int i = 0; i < M; i++) {
            HashNode<K, V> node = chainArray[i];
            while (node != null) {
                if (node.value.equals(value)) {
                    return node.key;
                }
                node = node.next;
            }
        }
        return null;
    }

    public void printBucketSize() {
        int all = 0;
        for (int i = 0; i < M; i++) {
            HashNode<K, V> node = chainArray[i];
            int elements = 0;
            while (node != null) {
                all++;
                elements++;
                node = node.next;
            }
            System.out.println("Bucket: "+ i + ", Elements: "+ elements);
        }
        System.out.println("All: " + all);
    }
}
```
