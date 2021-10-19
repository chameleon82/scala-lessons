```scala

  /** Convert TypeTag into Manifest
    * Doesn't not supported in Dotty
    *
    * credits: https://stackoverflow.com/questions/59637768/in-scala-2-11-reflection-how-to-reliably-convert-a-typetag-and-a-manifest-into/59673693#59673693
    * @return
    *   Manifest
    */
  private def toManifest[T: TypeTag]: Manifest[T] = {
    val tt = typeTag[T]
    val mirror = tt.mirror
    def toManifestRec(t: Type): Manifest[_] = {
      ClassTag[T](mirror.runtimeClass(t)) match {
        case ClassTag.Byte => Manifest.Byte
        case ClassTag.Short => Manifest.Short
        case ClassTag.Char => Manifest.Char
        case ClassTag.Int => Manifest.Int
        case ClassTag.Long => Manifest.Long
        case ClassTag.Float => Manifest.Float
        case ClassTag.Double => Manifest.Double
        case ClassTag.Boolean => Manifest.Boolean
        case ClassTag.Unit => Manifest.Unit
        case ClassTag.Object => Manifest.Object
        case ClassTag.Nothing => Manifest.Nothing
        case ClassTag.Null => Manifest.Null
        case classTag =>
          val clazz = classTag.runtimeClass
          if (t.typeArgs.nonEmpty) {
            val args = t.typeArgs.map(x => toManifestRec(x))
            ManifestFactory.classType(clazz, args.head, args.tail: _*)
          } else ManifestFactory.classType(clazz)
      }
    }
    toManifestRec(tt.tpe.dealias).asInstanceOf[Manifest[T]]
  }
```
