---
title: Common types
geekdocFlatSection: true
---

If more than one service requires the same data sctructure, it will be created as a common type.

Common types are imported from this [repo](https://git.windmaker.net/musicmanager/Common-Types).

## Artist

This type stores artist info:

```go
type Artist struct {
	Name    string
	URL     string
	ID      int
	Genre   string
	Country string
	Records []Record
}
```

When required services look for Artist info it will be stores in **ArtistInfo** type, because there can be more than one artist with the same name, **ArtistInfo** stores in **Data** first Artist found, the other artists will be stored in **ExtraData**.

```go
type ArtistInfo struct {
	Data      Artist
	ExtraData []Artist
}
```
As many common types, this ArtistInfo type must be able to be encoded in order to be sent through queues.

## Record

This type stores Records info:
```go
type RecordType int

const (
	FullLength RecordType = 1 << iota
	Demo
	EP
	Compilation
	Live
	BoxedSet
	Single
	Video
	Split
	Other
)

type Record struct {
	Name   string
	ID     int
	Year   int
	URL    string
	Type   RecordType
	Tracks []Track
}
```

## Track

This type store Tracks info:
```go
type Track struct {
	Name    string
	Hours   int
	Minutes int
	Seconds int
}
```

## Info retrieval

Info retrieval jobs uses the same type:
```gotype InfoRetrievalType int

const (
	ArtistName InfoRetrievalType = 1 << iota
	ArtistData
	AlbumName
	AlbumData
	AlbumWithArtistData
)

type InfoRetrieval struct {
	Type   InfoRetrievalType
	Data   []byte
	Artist string
	Album  string
}
```
## Job

All jobs, no matter its type, use this common type. 
```go
type JobType int

const (
	ArtistInfoRetrieval JobType = 1 << iota
	RecordInfoRetrieval
	JobInfoRetrieval
	Die
)

type Job struct {
	ID         int
	Finished   bool
	Status     bool
	Type       JobType
	LastOrigin string
	Data       []byte
	Result     []byte
	Error      string
}

```
