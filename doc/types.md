---
layout: doc
title: Type mappings
---

## Type mappings when reading values sent from the backend

PostgreSQL type         | Default .NET type             | Provider-specific type	| Other .NET types
------------------------|-------------------------------|-------------------------------|-----------------
bool			| bool				|				|
int2			| short				|				| byte, sbyte, int, long, float, double, decimal, string
int4			| int				|				| byte, short, long, float, double, decimal, string
int8			| long				|				| long, byte, short, int, float, double, decimal, string
float4			| float				|				| double
float8			| double			|				|
numeric			| decimal			|				| byte, short, int, long, float, double, string
money			| decimal			|				|
text			| string			|				| char[]
varchar			| string			|				| char[]
bpchar			| string			|				| char[]
citext			| string			|				| char[]
json			| string			|				| char[]
jsonb			| string			|				| char[]
xml			| string			|				| char[]
point			| NpgsqlPoint			|				| string
lseg			| NpgsqlLSeg			|				| string
path			| NpgsqlPath			|				|
polygon			| NpgsqlPolygon			|				|
line			| NpgsqlLine			|				| string
circle			| NpgsqlCircle			|				| string
box			| NpgsqlBox			|				| string
bit(1)			| bool				|				| BitArray
bit(n)			| BitArray			|				|
varbit			| BitArray			|				|
hstore			| IDictionary<string, string>	|				| string
uuid			| Guid				|				| string
cidr			| NpgsqlInet			|				| string
inet			| IPAddress			| NpgsqlInet			| string
macaddr			| PhysicalAddress		|				| string
tsquery			| NpgsqlTsQuery			|				|
tsvector		| NpgsqlTsVector		|				|
date			| DateTime			| NpgsqlDate			|
interval		| TimeSpan			| NpgsqlTimeSpan		|
timestamp		| DateTime			| NpgsqlDateTime		|
timestamptz		| DateTime			| NpgsqlDateTime		| DateTimeOffset
time			| TimeSpan			|				|
timetz			| DateTimeOffset		|				| DateTimeOffset, DateTime, TimeSpan
bytea			| byte[]			|				|
oid			| uint				|				|
xid			| uint				|				|
cid			| uint				|				|
oidvector		| uint[]			|				|
name			| string			|				| char[]
(internal) char		| char				|				| byte, short, int, long
geometry (PostGIS)	| PostgisGeometry		|				|
record			| object[]			|				|
composite types		| T				|				|
range subtypes		| NpgsqlRange<TElement>		|				|
enum types		| TEnum				|				|
array types		| Array	(of child element type)	|				|

The Default .NET type column specifies the data type `NpgsqlDataReader.GetValue` will return.

`NpgsqlDataReader.GetProviderSpecificValue` will return a value of a data type specified in the Provider-specific type column,
or the Default .NET type if there is no specialization.

The Other .NET types column specifies which other data types in which the value can be retrieved using
`NpgsqlDataReader.GetBoolean`, `.GetByte`, `.GetDouble` etc. or by `.GetFieldValue<T>`.

## Type mappings when sending parameters to the backend

There are three rules that decides what backend type a parameter will sent as.

1. If the parameter's `NpgsqlDbType` is set, it is used.
2. If the parameter's `DbType` is set, it is used.
3. Of neither of the above is set, the the backend type will be inferred based on the type the value has.

Note that for DateTime and NpgsqlDateTime, the `Kind` attribute tells wether to use `timestamp` or `timestamptz`.

Note that when `NpgsqlDbType` or `DbType` is set to a primitive type (bool, numbers and string),
most other primitive types are accepted since they all implement the IConvertible interface,
which is what Npgsql uses to convert the value to the target type.

NpgsqlDbType	| DbType		| PostgreSQL type	| Accepted .NET types
----------------|-----------------------|-----------------------|--------------------
Boolean		| Boolean		| bool			| bool, IConvertible
Smallint	| Int16			| int2			| short, IConvertible
Integer		| Int32			| int4			| int, IConvertible
Bigint		| Int64			| int8			| long, IConvertible
Real		| Single		| float4		| float, IConvertible
Double		| Double		| float8		| double, IConvertible
Numeric		| Decimal, VarNumeric	| numeric		| decimal, IConvertible
Money		| Currency		| money			| decimal, IConvertible
Text		| String, StringFixedLength, AnsiString, AnsiStringFixedLength	| text	| string, char[], char, IConvertible
Varchar		| 			| varchar		| string, char[], char, IConvertible
Char		|			| char			| string, char[], char, IConvertible
Citext		|			| citext		| string, char[], char, IConvertible
Json		|			| json			| string, char[], char, IConvertible
Jsonb		|			| jsonb			| string, char[], char, IConvertible
Xml		|			| xml			| string, char[], char, IConvertible
Point		|			| point			| NpgsqlPoint
LSeg		|			| lseg			| NpgsqlLSeg
Path		|			| path			| NpgsqlPath
Polygon		|			| polygon		| NpgsqlPolygon
Line		|			| line			| NpgsqlLine
Circle		|			| circle		| NpgsqlCircle
Box		|			| box			| NpgsqlBox
Bit		|			| bit			| BitArray, bool, string
Varbit		|			| varbit		| BitArray, bool, string
Hstore		|			| hstore		| IDictionary<string, string>
Uuid		|			| uuid			| Guid, string
Cidr		|			| cidr			| IPAddress, NpgsqlInet
Inet		|			| inet			| IPAddress, NpgsqlInet
MacAddr		|			| macaddr		| PhysicalAddress
TsQuery		|			| tsquery		| NpgsqlTsQuery
TsVector	|			| tsvector		| NpgsqlTsVector
Date		| Date			| date			| DateTime, NpgsqlDate, IConvertible
Interval	|			| interval		| TimeSpan, NpgsqlTimeSpan, string
Timestamp	| DateTime, DateTime2	| timestamp		| DateTime, DateTimeOffset, NpgsqlDateTime, IConvertible
TimestampTZ	| DateTimeOffset	| timestamptz		| DateTime, DateTimeOffset, NpgsqlDateTime, IConvertible
Time		| Time			| time			| TimeSpan, string
TimeTZ		|			| timetz		| DateTimeOffset, DateTime, TimeSpan
Bytea		| Binary		| bytea			| byte[], ArraySegment<byte>
Oid		|			| oid			| uint, IConvertible
Xid		|			| xid			| uint, IConvertible
Cid		|			| cid			| uint, IConvertible
Oidvector	|			| oidvector		| uint[]
Name		|			| name			| string, char[], char, IConvertible
InternalChar	|			| (internal) char	| byte, IConvertible
Geometry	|			| geometry		| PostgisGeometry
Composite	|			| composite types	| T
Range \| (other NpgsqlDbType) |		| range types		| NpgsqlRange<TElement>
Enum		|			| enum types		| TEnum
Array \| (other NpgsqlDbType) | 	| array types		| Array, IList<T>, IList

Notes when using Range and Array, bitwise-or NpgsqlDbType.Range or NpgsqlDbType.Array with the child type.
For example, to construct the NpgsqlDbType for a `int4range`, write `NpgsqlDbType.Range | NpgsqlDbType.Integer`.
To construct the NpgsqlDbType for an `int[]`, write `NpgsqlDbType.Array | NpgsqlDbType.Integer`.

For information about enums, [see the enum page](enums.html).

| .NET type					| Auto-inferred PostgreSQL type
|-----------------------------------------------|------------------------------
| bool						| bool
| byte						| int2
| sbyte						| int2
| short						| int2
| int						| int4
| long						| int8
| float						| float4
| double					| float8
| decimal					| numeric
| string					| text
| char[]					| text
| char						| text
| NpgsqlPoint					| point
| NpgsqlLSeg					| lseg
| NpgsqlPath					| path
| NpgsqlPolygon					| polygon
| NpgsqlLine					| line
| NpgsqlCircle					| circle
| NpgsqlBox					| box
| BitArray					| varbit
| Guid						| uuid
| IPAddress					| inet
| NpgsqlInet					| inet
| PhysicalAddress				| macaddr
| NpgsqlTsQuery					| tsquery
| NpgsqlTsVector				| tsvector
| NpgsqlDate					| date
| NpgsqlDateTime(Kind=Local,Unspecified)	| timestamp
| NpgsqlDateTime(Kind=Utc)			| timestamptz
| DateTime(Kind=Local,Unspecified)		| timestamp
| DateTime(Kind=Utc)				| timestamptz
| DateTimeOffset				| timestamptz
| TimeSpan					| time
| byte[]					| bytea
| PostgisGeometry 				| geometry
| Custom composite type				| composite types
| NpgsqlRange<TElement>				| range types
| Enum types					| enum types
| Array types					| array types
