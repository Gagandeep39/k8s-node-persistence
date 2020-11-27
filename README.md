# Creating Volumes

1. Create volume at container level (inside spec)

```yml
# Empty Dir
volumes:
    - name: story-volume
      # Creates an empty directory
      # Removed only when pod is removed (It can survive restart, crash)
      emptyDir:  {}
```
```yml
# OR Host Path
volumes:
  - name: story-volume
    hostPath:
      path: /data
      type: DirectoryOrCreate
```
2. Add volume to container (Inside contaier)

```yml
volumeMounts:
   - mountPath: /app/story
    name: story-volume
```

- `emptyDir` Attached to a single pod
- `hostDir` Mapped to path in host machine. Can be attached to multiple pod

## CSI (Container Storage Interface)

- Allows attaching any type of storage to Kubernets
- We need to manually write integration Code (Lookup github for drivers to use them)

## Persisten Volume

- Object provided by Kubernete
- Can be claimed by Pods
```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: host-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: FileSystem
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data
    type: DirecotryOrCreate

```

## Persistence Volume Caim

- Part of Volume that can be claimed by a pod
```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: host-pvc
  spec:
    VolumeName: host-pv
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        # Must be less than Persistent Volume
        storage: 1Gi
```

## Using it

- Add below code inside spec
```yml
volumes:
  - name: story-volume
    persistentVolumeClaim:
        claimName: host-pvc
```

## Volumes vs PersistenceVolume

### Volume

- Attached to pod lifecycle
- Defined and created with pod
- Diffeicult to manage at administrator level

### Persistence Volume (Better)

- Standalone
- Claimed via PVC
- Can be defined once and multiple times
